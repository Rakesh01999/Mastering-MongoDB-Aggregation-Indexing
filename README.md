
---

# MongoDB Aggregation Framework and Indexing Practice

This project explores various MongoDB aggregation stages and indexing methods, providing practical examples to build a strong understanding of data manipulation and optimization techniques.

## Table of Contents

1. [Introduction to Aggregation Framework](#introduction-to-aggregation-framework)
2. [Aggregation Stages](#aggregation-stages)
   - [$match and $project](#match-and-project)
   - [$addFields, $out, and $merge](#addfields-out-and-merge)
   - [$group, $sum, and $push](#group-sum-and-push)
   - [$unwind](#unwind)
   - [$bucket, $sort, and $limit](#bucket-sort-and-limit)
   - [$facet for Multi-Pipeline Processing](#facet-for-multi-pipeline-processing)
   - [$lookup for Data Join](#lookup-for-data-join)
3. [Indexing in MongoDB](#indexing-in-mongodb)
4. [Practice Data](#practice-data)
5. [Additional Resources](#additional-resources)

## Introduction to Aggregation Framework

The MongoDB Aggregation Framework allows data processing pipelines for transforming and aggregating data within a collection. It's a powerful tool for advanced data analysis and manipulation.

### Key Resources
- [MongoDB Aggregation Framework Documentation](https://www.mongodb.com/docs/manual/aggregation/)
- [Aggregation Pipeline Operators](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/)

## Aggregation Stages

### $match and $project

The `$match` stage filters documents based on specified criteria. `$project` controls which fields to include or exclude in the output.

Example:
```js
db.test.aggregate([
    { $match: { gender: "Male", age: { $lt: 35 } } },
    { $project: { name: 1, age: 1, gender: 1 } }
]);
```

### $addFields, $out, and $merge

- `$addFields` adds new fields to documents.
- `$out` writes the result into a new collection.
- `$merge` merges results into an existing collection.

Example:
```js
db.test.aggregate([
    { $match: { gender: "Male", age: { $lt: 35 } } },
    { $addFields: { course: "level-2" } },
    { $project: { name: 1, gender: 1, course: 1 } },
    { $out: "course-students" }
]);
```

### $group, $sum, and $push

- `$group` groups documents by specified fields.
- `$sum` calculates the total for a numeric field.
- `$push` creates an array of values.

Example:
```js
db.test.aggregate([
    { $group: { _id: "$gender", count: { $sum: 1 } } }
]);
```

### $unwind

`$unwind` deconstructs an array field from the input documents, outputting a document for each element in the array.

Example:
```js
db.test.aggregate([
    { $unwind: "$friends" },
    { $group: { _id: "$friends", count: { $sum: 1 } } }
]);
```

### $bucket, $sort, and $limit

- `$bucket` groups documents based on specified boundaries.
- `$sort` orders documents by specified fields.
- `$limit` restricts the output to a specified number of documents.

Example:
```js
db.test.aggregate([
    { $bucket: {
        groupBy: "$age",
        boundaries: [20, 40, 60, 80],
        default: "greater than 80",
        output: {
            count: { $sum: 1 },
            users: { $push: "$name" }
        }
    }},
    { $sort: { count: -1 } },
    { $limit: 2 }
]);
```

### $facet for Multi-Pipeline Processing

`$facet` allows running multiple pipelines within a single aggregation stage, useful for aggregating data differently in parallel.

Example:
```js
db.test.aggregate([
    { $facet: {
        "friendsCount": [
            { $unwind: "$friends" },
            { $group: { _id: "$friends", count: { $sum: 1 } } }
        ],
        "educationCount": [
            { $unwind: "$education" },
            { $group: { _id: "$education", count: { $sum: 1 } } }
        ]
    }}
]);
```

### $lookup for Data Join

`$lookup` joins documents from two collections.

Example:
```js
db.orders.aggregate([
    {
        $lookup: {
            from: "test",
            localField: "userId",
            foreignField: "_id",
            as: "user"
        }
    }
]);
```

## Indexing in MongoDB

Indexes improve query performance by reducing the amount of data MongoDB needs to scan. You can view the query execution plan using `.explain()`.

Example:
```js
db.test.find({ email: "example@example.com" }).explain("executionStats");
```

### Key Resources
- [MongoDB Indexing Documentation](https://www.mongodb.com/docs/manual/indexes/)
- [COLLSCAN vs IXSCAN](https://www.mongodb.com/docs/manual/tutorial/analyze-query-plan/)

## Practice Data

To practice these aggregation techniques, you can load sample data from [massive-data.json](https://raw.githubusercontent.com/Apollo-Level2-Web-Dev/mongodb-practice/main/massive-data.json).

## Additional Resources

- [MongoDB Aggregation Framework](https://www.mongodb.com/docs/manual/aggregation/)
- [Aggregation Pipeline Operators](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/)
- [Indexing in MongoDB](https://www.mongodb.com/docs/manual/indexes/)
- [MongoDB Aggregation Framework - Studio 3T](https://studio3t.com/knowledge-base/articles/mongodb-aggregation-framework/)

---

