# Working with Relations

Created: July 8, 2022 2:41 PM
Finished: No
Source: https://orkhan.gitbook.io/typeorm/docs/relational-query-builder

![-LtnC2ol3a7L23H5bm0F.png](Working%20with%20Relations%20b420aac80322476fa537f5ff3aec2574/-LtnC2ol3a7L23H5bm0F.png)

`RelationQueryBuilder` is a special type of `QueryBuilder` which allows you to work with your relations. Using it, you can bind entities to each other in the database without the need to load any entities, or you can load related entities easily.

Also, another benefit of such an approach is that you don't need to load every related entity before pushing into it. For example, if you have ten thousand categories inside a single post, adding new posts to this list may become problematic for you, because the standard way of doing this is to load the post with all ten thousand categories, push a new category, and save it. This results in very heavy performance costs and is basically inapplicable in production results. However, using `RelationQueryBuilder` solves this problem.