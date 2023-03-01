[
  {
    $lookup: {
      from: "persons",
      localField: "_id",
      foreignField: "_id",
      as: "person",
    },
  },
  {
    $unwind: "$person",
  },
  {
    $lookup: {
      from: "businessRelationships",
      pipeline: [
        {
          $match: {
            sellerId: ObjectId(
              "62c42a955a40ed54ffddb0b5"
            ),
          },
        },
      ],
      localField: "_id",
      foreignField: "personId",
      as: "relationship",
    },
  },
  {
    $lookup: {
      from: "stores",
      //localField: "relationship.origin.storeId",
      localField: "relationship.sellerId",
      foreignField: "sellerId",
      as: "store",
    },
  },
  {
    $match: {
      "store.sellerId": ObjectId(
        "62c42a955a40ed54ffddb0b5"
      ),
    },
  },
  {
    $lookup: {
      from: "channelOrderTakers",
      localField: "store._id",
      foreignField: "storeId",
      as: "channelOrderTaker",
    },
  },
  {
    $unwind: "$channelOrderTaker",
  },
  {
    $lookup: {
      from: "orders",
      let: {
        channel_id: "$channelOrderTaker._id",
      },
      pipeline: [
        {
          $unwind: "$orderSplitting",
        },
        {
          $match: {
            $and: [
              {
                "orderSplitting.orderStages": {
                  $not: {
                    $elemMatch: {
                      stage: 7,
                    },
                  },
                },
              },
              {
                "registryOptions.status": 1,
              },
              {
                $expr: {
                  $eq: [
                    "$channelId",
                    "$$channel_id",
                  ],
                },
              },
            ],
          },
        },
      ],
      localField: "_id",
      foreignField: "buyerId",
      as: "order",
    },
  },
  {
    $addFields: {
      ordersCount: {
        $size: "$order",
      },
      ordersSubTotal: {
        $reduce: {
          input:
            "$order.orderSplitting.untrackedProducts.moneyTotal",
          initialValue: 0,
          in: {
            $add: [
              "$$value",
              {
                $first: "$$this",
              },
            ],
          },
        },
      },
      ordersDiscount: {
        $reduce: {
          input:
            "$order.orderSplitting.discounts",
          initialValue: 0,
          in: {
            $cond: {
              if: {
                $or: [
                  {
                    $eq: [
                      {
                        $first:
                          "$$this.discountType",
                      },
                      1,
                    ],
                  },
                  {
                    $eq: [
                      {
                        $first:
                          "$$this.discountType",
                      },
                      2,
                    ],
                  },
                ],
              },
              then: {
                $add: [
                  "$$value",
                  {
                    $ifNull: [
                      {
                        $first:
                          "$$this.appliedDiscount",
                      },
                      0,
                    ],
                  },
                ],
              },
              else: "$$value",
            },
          },
        },
      },
    },
  },
  {
    $addFields: {
      ordersTotal: {
        $subtract: [
          "$ordersSubTotal",
          "$ordersDiscount",
        ],
      },
    },
  },
]