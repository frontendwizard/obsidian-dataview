# DQL, JS and Inlines

Once you've added [useful data to relevant pages](../annotation/add-metadata.md), you'll want to actually display it somewhere or operate on it. Dataview
allows this in four different ways, all of which are written in codeblocks directly in your Markdown and live-reloaded
when your vault changes.

## Dataview Query Language (DQL)

The [**Dataview Query Language**](../../queries/structure) (for short **DQL**) is a SQL-like language and Dataviews core functionality. It supports [four Query Types](./query-types.md) to produce different outputs, [data commands](./data-commands.md) to refine, resort or group your result and [plentiful functions](../reference/functions.md) which allow numerous operations and adjustments to achieve your wanted output. 

You create a **DQL** query with a codeblock that uses `dataview` as type:

~~~
```dataview
TABLE rating AS "Rating", summary AS "Summary" FROM #games
SORT rating DESC
```
~~~

!!! attention "Use backticks"
    A valid codeblock needs to use backticks (\`) on start and end (three each). Do not confuse the backtick with the similar looking apostrophe ' !

Find a explanation how to write a DQL Query under the [query language
reference](../../queries/structure). If you learn better by example, take a look at the [query examples](../../resources/examples).

## Inline DQL

A Inline DQL uses a inline block format instead of a code block and a configurable prefix to mark this inline code block as a DQL block.

~~~
`= this.file.name`
~~~

!!! info "Change of DQL prefix"
    You can change the `=` to another token (like `dv:` or `~`) in Dataviews' settings under "Codeblock Settings" > "Inline Query Prefix"

Inline DQL Queries display **exactly one value** somewhere in the middle of your note. They seamlessly blend into the content of your note:

~~~markdown
Today is `= date(today)` - `= [[exams]].deadline - date(today)` until exams!
~~~

would, for example, render to 

~~~markdown
Today is November 07, 2022 - 2 months, 5 days until exams!
~~~

**Inline DQL** cannot query multiple pages. They always display exactly one value, not a list (or table) of values. You can either access the properties of the **current page** via prefix `this.` or a different page via `[[linkToPage]]`.

~~~markdown
`= this.file.name`
`= this.file.mtime`
`= this.someMetadataField`
`= [[secondPage]].file.name`
`= [[secondPage]].file.mtime`
`= [[secondPage]].someMetadataField`
~~~

You can use everything available as [expressions](../../reference/expressions) and [literals](../../reference/literals) in a Inline DQL, including [functions](../../reference/functions). Query Types and Data Commands on the other hand are **not available in Inlines.**

!!! hint "Saving Inline DQL in metadata fields"
    You can store a Inline DQL inside a field for reusability with some limitations. Read more about it in [the FAQ](../../resources/faq#can-i-save-the-result-of-a-query-for-reusability).

## Dataview JS

The dataview [JavaScript API](../../api/intro) gives you the full power of JavaScript and provides a DSL for pulling
Dataview data and executing queries, allowing you to create arbitrarily complex queries and views. Similar to the query
language, you create Dataview JS blocks via a `dataviewjs`-annotated codeblock:

~~~java
```dataviewjs
let pages = dv.pages("#books and -#books/finished").where(b => b.rating >= 7);
for (let group of pages.groupBy(b => b.genre)) {
   dv.header(3, group.key);
   dv.list(group.rows.file.name);
}
```
~~~

Inside of a JS dataview block, you have access to the full dataview API via the `dv` variable. For an explanation of
what you can do with it, see the [API documentation](../../api/code-reference), or the [API
examples](../../api/code-examples).

!!! attention "Advanced usage"
    Writing Javascript queries is a advanced technique that requires understanding in programming and JS. Please be aware that JS Queries have access to your file system and be cautious when using other peopless' JS Queries, especially when they are not publicy shared through the Obsidian Community.

## Inline Dataview JS

Similar to the query language, you can write JS inline queries, which let you embed a computed JS value directly. You
create JS inline queries via inline code blocks:

```
`$= dv.current().file.mtime`
```

In inline DataviewJS, you have access to the `dv` variable, as in `dataviewjs` codeblocks, and can make all of the same calls. The result
should be something which evaluates to a JavaScript value, which Dataview will automatically render appropriately.

Unline Inline DQL queries, Inline JS queries do have access to everything a Dataview JS Query has available and can hence query and output multiple pages.

!!! info "Change of Inline JS prefix"
    You can change the `$=` to another token (like `dvjs:` or `$~`) in Dataviews' settings under "Codeblock Settings" > "Javascript Inline Query Prefix"