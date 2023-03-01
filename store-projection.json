[
  {
    $match: {
      "registryOptions.status": 1,
      _id: ObjectId("63337e540a3f21803ddcad22"),
    },
  },
  {
    $lookup: {
      from: "channelOrderTakers",
      localField: "_id",
      foreignField: "storeId",
      as: "orderTaker",
    },
  },
  {
    $unwind: "$orderTaker",
  },
  {
    $lookup: {
      from: "channelMktplaces",
      localField: "_id",
      foreignField: "storeId",
      as: "mkt",
    },
  },
  {
    $unwind: "$mkt",
  },
  {
    $lookup: {
      from: "channelShowrooms",
      localField: "_id",
      foreignField: "storeId",
      as: "showroom",
    },
  },
  {
    $project: {
      name: 1,
      description: 1,
      segment: 1,
      orderTaker: 1,
      mkt: 1,
      showroom: 1,
    },
  },
]