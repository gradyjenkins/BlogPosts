# Animations in Swift using UIViewPropertyAnimator

1. Intro - Background on animations in Swift
2. Explanation of block-based animations
3. Introduce UIViewPropertyAnimator
4. v

## Introduction 
The mobile app martketplace is full of beautiful designs and eye-popping animations that capture user's attention and increase user satisfaction. Adding beautiful animations within a mobile app can be the difference maker between your app and a competitor. 

Apple provides many different options and APIs to add beautiful animations to your app. Each API has it's own benefits and it's up to the user to pick which one they want to use. 

#### UIView.Animate()

The first API that can be used is `UIView.animate()`. Probably the most basic way to create animations, `animate()` allows you to modify some properties on a `UIView` instance. According to Apple's documentation<sup>1</sup> , these properties are `frame`, `bounds`, `center`, `transform`, `alpha`, and `backgroundColor`. By interacting with these properties in an animation block you can change your view's appearance. To change a view's background color, all you would need to do is:

```swift
let myView = UIView()

myView.backgroundColor = .red

myView.animate(withDuration: 1.5) {
    myView.backgroundColor = .green
}
```

This code will change the view's background color from red to green in a span of 1.5 seconds. This is a very simple way to animate a property on your view, but it is actually discouraged by Apple in favor of using the animation API that was introduced in iOS 10, `UIViewPropertyAnimator`. Both `UIView.animate()` and `UIViewPropertyAnimator` take advantage of a number of the same parameters such as `delay` and `duration`. 

## UIViewPropertyAnimator

The introduction of `UIViewPropertyAnimator` added a new animation API which allowed for more fluid chaining of animations and the ability to dynamically modify those animations while they are in progress. 

Before `UIViewPropertyAnimator` was introduced, chaining animations required an ugly pyramid of code. 

```swift
let myView = UIView()

myView.backgroundColor = .red

UIView.animate(withDuration: 1.5, animations: {
    myView.backgroundColor = .green
}, completion: { _ in
    UIView.animate(withDuration: 1, animations: {
        myView.transform = CGAffineTransform(scaleX: 2, y: 2)
    })
})
```

This indentation would occur with every animation you wanted to chain and can get messy very quickly. `UIViewPropertyAnimator` solves this by providing an API to add completions which contains `UIViewAnimatingPosition`<sup>2</sup> to determine the current animation's progress.



## Sources
[1](https://developer.apple.com/documentation/uikit/uiview) - UIView documentation

[2](https://developer.apple.com/documentation/uikit/uiviewanimatingposition) - UIViewAnimatingPosition documentation