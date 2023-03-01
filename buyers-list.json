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
    $lookup: {
      from: "orders",
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
  {
    $project: {
      "person.legalName": 1,
      "person.documents": 1,
      "person.contacts": 1,
      "person.adresses": 1,
      "relationship.origin.channelType": 1,
      "store._id": 1,
      "store.name": 1,
      "registryOptions.creationDate": 1,
      "registryOptions.status": 1,
      ordersCount: 1,
      ordersTotal: 1,
      primaryAddress: {
        $first: {
          $filter: {
            input: "$person.adresses",
            as: "address",
            cond: {
              $eq: ["$$address.primary", true],
            },
          },
        },
      },
    },
  },
]