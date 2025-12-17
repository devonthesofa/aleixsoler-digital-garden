---
tags: []
share:
  - "true"
---
IMO this is more of a design decision and their really is no wrong way to do it. So it all depends on the requirements.

If you decide that deleting a category deletes all the child post elements, then you can do that in multiple way (ordered by my preference).

- Control that cascading deletes in the database.
- Add code in the DAL layer so that when a category is called, it deletes all the post under the category.

If you decide to not do the "cascade" delete for the child post, then your only option is to return an appropriate error message stating why the category cannot be deleted.

If you want you can make it more clear what the call to the web service does by doing something like this.

DELETE /category/1?includePost=true --> Deletes category #1 & all post under it.

DELETE /category/1 --> Delete category #1 or returns error if it can't delete it.


https://stackoverflow.com/questions/48519825/in-a-restful-api-should-delete-calls-be-recursive