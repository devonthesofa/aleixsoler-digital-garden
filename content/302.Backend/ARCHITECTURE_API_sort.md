---
tags: []
share:
  - "true"
---
```
There is not the "best" way to do that. For any API, basically it's more important to have consistency, follow the same strategy across the endpoints and be flexible enough to future improvements.

So, we have some options. For example, if you need to order by title with DESC and by author with ASC, you can choose:

/api/blogs?sort=-title,+author   GET /products?sort=-popularity,price

/api/blogs?sort=desc(title),asc(author)
 
/api/blogs?sort=title:desc,author:asc
Some people use the format used in your example. But it's have the limitation to not be flexible enough to allow the sort by multiple fields, because sort and order are separated.
```

## Referencias:
- https://stackoverflow.com/questions/56516021/ordering-and-sorting-in-restful-api
- https://www.atatus.com/blog/rest-api-design-filtering-sorting-and-pagination/