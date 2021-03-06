# Basic API

Find, update and delete records.

The examples use the following prisma schema:

```prisma
model Post {
    id        String   @default(cuid()) @id
    createdAt DateTime @default(now())
    updatedAt DateTime @updatedAt
    published Boolean
    title     String
    content   String?

    comments Comment[]
}

model Comment {
    id        String   @default(cuid()) @id
    createdAt DateTime @default(now())
    content   String

    post   Post @relation(fields: [postID], references: [id])
    postID String
}
```

## Reading data

### Find many records

```go
posts, err := client.Post.FindMany(
    db.Post.Title.Equals("hi"),
).Exec(ctx)
```

If no records are found, the query above returns an slice array without returning an error (like usual SQL queries).

### Find one record

```go
post, err := client.Post.FindOne(
    db.Post.ID.Equals("123"),
).Exec(ctx)

if err == db.ErrNotFound {
    log.Printf("no record with id 123")
}
```

This returns an `ErrNotFound` error (exported by the generated client) if there was no such record.

### Query API

The query operations change based on the data types in your schema. For example, integers and floats will have greater than and less than operations, while strings have prefix and suffix operations.

```go
post, err := client.Post.FindOne(
    // query for posts containing the title "hi"
    db.Post.Title.Contains("what up"),
).Exec(ctx)
```

To explore more query filters, see [all possible query filters](./03-filters.md).

### Querying for relations

You can query for relations by using "Some" or "Every" to query for records where only some or all of the records match respectively. You can nest those queries as deep as you like.

```go
// get posts which have at least one comment with a title "My Title" and that post's comments are all "What up?"
posts, err := client.Post.FindMany(
    Post.Title.Equals("what up"),
    Post.Comments.Some(
        Comment.Title.Equals("My Title"),
    ),
).Exec(ctx)
```

To explore querying for relations in detail, see [more relation query examples](./08-relations.md).

## Next steps

Read the next article [query filters](./03-filters.md) to explore how to form more complex queries.
