# iOS布局constraints

## 左右上下

```objc
Red View.leading = Safe Area.leading + 10
Safe Area.trailing = Red View.trailing + 10
Red View.top = Safe Area.top + 10
Safe Area.bottom = Red View.bottom + 10
```

## demo

```objc
Red View.Leading = Superview.LeadingMargin
Red View.Trailing = Superview.TrailingMargin
Red View.Top = Top Layout Guide.Bottom + 20.0
Bottom Layout Guide.Top = Red View.Bottom + 20.0
```





# todo

- [ ] 如何手动设置constraints，而非拖拽