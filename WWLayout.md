# WWLayout

## Introduction

There are many options when it comes to building the layout of an iOS app and everyone has their own preferences. The two most popular ways are using Interface Builder and writing out your views programmatically. Each has their own way of creating view elements and specifying their constraints, one using Storyboards and the other writing everything in code. At WW, we write our views programmatically and specify constraints using a Swift DSL called WWLayout that was written in house by one of our iOS engineers, Steven Grosmark.

Everyone that has built an iOS app using programmatic layouts understands the pains that come with it. Forgetting to add `translatesAutoresizingMaskIntoConstraints = false` or forgetting to activate the constraints can cause headaches when your view doesn’t behave as you expect. This comes with a lot of boilerplate code that can end up making your views and view controllers hard to read. A complicated view with multiple elements scattered around the screen can add hundreds of lines of code to your project. 

### Background

To provide some background on the development process and reasoning behind creating the library I reached out to the author, Steven Grosmark. 

> About a year and a half ago, we (WW) were considering how to standardize our layout code.  The options at the time were native constrains, visual layout (not an option, but we still had code using this), PureLayout, and SnapKit. Native constrains were overly verbose, visual format was too string-y, PureLayout wasn't very swifty and not well maintained, and I wasn't crazy about SnapKit adding associated objects to every view that was laid out.  So I started exploring different kinds of syntax, from the perspective of "what kind of code do I want to be able to write?"  After a few experiments, the method of having a lightweight `layout` property available to all UIViews felt about right.  With that figured out, it was just a matter of adding access to all the possible combinations of edges, constants and priorities. -Steven Grosmark
>
When I've used packages in my projects in the past I've always preferred lightweight solutions and that's why I've enjoyed working with WWLayout since I started at WW. As you can see from the quote above WWLayout was designed around being as lightweight as possible.

## Quickstart

WWLayout solves this problem by allowing users to write constraints in a readable fashion. WWLayout takes care of all of the boilerplate code that is needed to create views programmatically. You no longer have to toggle the `translatesAutoresizingMaskIntoConstraints` flag or set `isActive = true` for every new element added to your view, as WWLayout handles both of these. 

#### Installation

1. Add `pod 'WWLayout'` to your Podfile.
2. Run `pod install` to install the new pod.
3. Add `import WWLayout` to your file you want to layout views.

#### Usage

WWLayout adds a `layout` property on UIView which is the main point of entry for the library. All constraints will be added using this property: 

`myView.layout.center(in: .superview)`

The layout property holds a reference to the view element which allows for constraints to be chained one after another. Chaining constraints is as simple as: 

`myView.layout.width(300).height(400)`

An example of centering a view element within a parent view is as easy as:
```swift
let button = UIButton()

override func viewDidLoad() {
    super.viewDidLoad()
    
    view.addSubview(button)
    button.setTitle("Click me", for: .normal)
    button.setTitleColor(.black, for: .normal)
    button.layout.center(in: .superview)
}
```
<p align='center'>
    <img src='https://i.imgur.com/9BqYo60.png'>
</p>

A slightly more complicated view with two labels and a button:
```swift
let button = UIButton()
let titleLabel = UILabel()
let subtitleLabel = UILabel()

titleLabel.layout
    .top(to: .superview, offset: 50)
    .centerX(to: .superview)
        
subtitleLabel.layout
    .leading(.greaterOrEqual, to: titleLabel, edge: .trailing, offset: 40, priority: .low)
    .trailing(to: .superview, edge: .trailing, offset: 10, priority: .high)
    .lastBaseline(to: titleLabel)

button.layout
    .below(titleLabel, offset: 30)
    .fillWidth(of: .superview, maximum: 315)
```

<p align='center'>
    <img src='https://imgur.com/qNJqJ1A.png'>
</p>

Let's break this view down. There are three view elements, a button, and two labels. As you can see, some of the constraints above contain `.superview` as the . WWLayout provides a `.superview` convenience instead of typing out the name of whatever the parent view is.

For `titleLabel`, I'm only constraining the top edge to be 50 points off of the top edge of the view and centered horizontally. 

The leading edge of `subtitleLabel` is then constrained to `titleLabel`'s trailing edge with optional parameters for relation set to `.greaterOrEqual` and priority set to `.low`. The trailing edge of `subtitleLabel` is then constrained to the trailing edge of the `.superview`. We mark this priority as `.high` in order to take precedence over the leading edge constraint, which means that the trailing edge constraint will be fulfilled before the leading edge constraint.

Finally, the button is simply constrained to be below `titleLabel` with an `offset` of 30 and it will fill the width of the `.superview` with a maximum width of 315. 

Every function has a number of optional parameters such as `priority`, `relation`, `edge`, `inset`, and `offset`. These optional parameters allow you to fully customize your constraints to create your desired layout.

#### .safeArea

Currently when adding constraints against the Safe Area you will need to do a check to see if the phone's operating system supports it. This can cause the codebase to be littered with checks such as: 

```if #available(iOS 11.0, *)```

WWLayout provides a convenience that removes this check by using `.safeArea`. `.safeArea` will add constraints to the phone's safe area layout guide and fall back to `.superview` if they are not available.

#### fill()

One function that is incredibly powerful is `fill()`. `fill` comes with a couple optional parameters - `axis:` and `except:`. `axis` allows you to specify that you want your view to fill all available space in either the horizontal `.x` or vertical `.y` plane. `except` allows you to specify that you want the view to fill all available edges except for a specified one on the view. I routinely use `fill` to build the views that I work on and it always proves to be useful.

### Evolution

The next big thing is being able to tag constraints to easily switch between various layouts dynamically.  This happens to open up the ability to specify constraints per size class, so that your layout can automatically change when a device is rotated, for example.  There is a PR open that adds both of these features - it currently needs some use-case testing and documentation, but is otherwise ready to go.

## Conclusion

WWLayout is an incredibly powerful, lightweight, and readable way to write constraints for your views. It provides a boost in productivity by reducing the amount of boilerplate code you need to write in order to construct a view and allows for other maintainers to quickly understand the constraints later on. 

WWLayout is open source and you can contribute or find the full reference at the [GitHub repository](https://github.com/ww-tech/wwlayout).