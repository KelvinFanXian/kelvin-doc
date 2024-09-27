# ref

- [jsonpath](https://www.jsdelivr.com/package/npm/jsonpath)



## JSONPath Syntax

> Query JavaScript objects with JSONPath expressions. Robust / safe JSONPath engine for Node.js.


|JSONPath |	Description |
| ---- | ---- |
|$	| The root object/element |
|@	| The current object/element |
|.	| Child member operator |
|..	| Recursive descendant operator; JSONPath borrows this syntax from E4X |
|*	| Wildcard matching all objects/elements regardless their names |
|[]	| Subscript operator |
|[,]	| Union operator for alternate names or array indices as a set |
|\[start: end :step]	| Array slice operator borrowed from ES4 / Python |
|?()	| Applies a filter (script) expression via static evaluation |
|()	| Script expression via static evaluation |



# in action

```javascript
// 引入
function _jsonpath(){
    let s = document.createElement('script');
    s.src='https://cdn.jsdelivr.net/npm/jsonpath@1.1.1/jsonpath.min.js';
    document.head.append(s);
}
_jsonpath();

```

