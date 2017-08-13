---
layout:     post
title:      React-router-dom bindings for Reason
date:       2017-08-18 16:32:11
categories: reason
---

[This repository](https://github.com/dariooddenino/bs-react-router-dom) contains simple bucklescript bindings for [react-router-dom](https://www.npmjs.com/package/react-router-dom) v4.

There are some small differences in usage, mainly depending on how reason-react wants components to be passed as props.

The `Route` component expects a `componentMake` containing the wrapped component's `make` function.

```
<Route path="/2" componentMake=Foo.make />
```

For components like `Link` or `NavLink` which expect a `to` prop containing either a string or an object, you'll have to pass either a `_to: string` or a `toObj: Js.t ({.. pathname: string} as 'a)`.

```
<NavLink _to="/"> ... </NavLink>
<NavLink toObj={"pathname": "/2"}> ... </NavLink>
```

Please not that this never intended to be a complete implementation of the library and that it was written as an exercise.
Despite that, it works correctly except for a couple of missing things:

- `matchPath`
- `withRouter`
- some types need to be improved