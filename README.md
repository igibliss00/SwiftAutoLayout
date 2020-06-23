# Auto Layout

This app demonstrates the two different ways of programmatically implementing auto layout into an app without the Interface Builder: Visual Format Langue and Auto Layout Anchors.  

## Installing

```
git clone http://www.github.com/igibliss00/SwiftAutoLayout
```

## Features

### Visual Format Language

Auto Layout is Swift's way of addressing the design issue of accommodating screens of multiple different sizes, whether it be in different iPhones or iPads.  It's also a way to deal with the portrait/landscape mode.  Ideally, you want your design to look proportional according to the screen size, but, if not, at least not have the elements be outside of the viewport.  

The Interface Builder has the visual way of setting the Auto Layout constraints, but sometimes we want to have constraints be more reusuable.  On top of that, it's sometimes more desirable to have the constraints in a written form so that it's more readable to other developers.  This is where Visual Format Language comes in.  

Visual Format Language is a way to build programmatic constraints using visual syntax strings.  It's to specify common constraints like spacing and vertical layout, and constraints with different priorities.  It's visual in the sense that the notation visually matches the image.  This allows developers to read the constraints, say, on Github or on text editors without the requirement of the Interface Builder.   

```
let viewsDictionary = ["label1": label1, "label2": label2, "label3": label3, "label4": label4, "label5": label5]
for label in viewsDictionary.keys {
   view.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "H:|[\(label)]|", options: [], metrics: nil, views: viewsDictionary))
}
view.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "V:|[label1]-[label2]-[label3]-[label4]-[label5]", options:[], metrics: nil, views: viewsDictionary))
```
<img src="https://github.com/igibliss00/SwiftAutoLayout/blob/master/README_assets/1.png" width=400/>

The initial constraints simply set the labels to be horizontally filled and spaced out evenly vertically.  And, then, progressively added a few more constraints.  

```
 let metrics = ["labelHeight": 88]
        
view.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "V:|[label1(labelHeight@999)]-[label2(label1)]-[label3(label1)]-[label4(label1)]-[label5(label1)]-(>=10)-|", options:[], metrics: metrics, views: viewsDictionary))
```

- First, “-(>-10)-“ is added to the very end to signify the bottom margin of at least 10 vertically.  
- Second, the fixed label height of 88 points was given to all the labels
- Now that the top margin, bottom margin, and the label heights are all set, the display looks fine in a portrait mode.  However, "Unable to simultaneously satisfy constraints" message shows when the device is rotated horizontally.  This is because the height of the device is too narrow to satisfy all three constraints that’s been just set. 
- Set the label height’s priority at 999.  Priority in constraint is a contingency plan which is to be executed in an absolute manner or in a secondary manner, depending on the priority, in the case the constraints conflict. @1000 is the absolute necessary priority, so by setting the height of the labels at @999, it’s saying that if the top margin and the bottom margin is set, but the screen is too narrow to fulfill the height of 88 points, be flexible to accommodate it. 

<img src="https://github.com/igibliss00/SwiftAutoLayout/blob/master/README_assets/2.png" width=400/>
<img src="https://github.com/igibliss00/SwiftAutoLayout/blob/master/README_assets/3.png" height=400/>


### Auto Layout Anchors

Another way of programmatically implement constraints is to use the anchors.  I’d say anchors are the least visual way to apply constraints, but also the least verbose way.  Anchors can be categorized into horizontal layout layouts, vertical layout anchors, and dimension layout anchors. 

#### Horizontal Layout Anchors
1. Leading anchor
2. Left anchor
3. Trailing anchor
4. Right anchor
5. Center-X anchor

#### Vertical Layout Anchors
1. Top anchor
2. Bottom anchor
3. Center-Y anchor
4. First baseline anchor
5. Last baseline anchor

#### Dimension Layout Anchors
1. Height anchor
2. Width anchor

```
var previous: UILabel?

for label in [label1, label2, label3, label4, label5] {
    label.widthAnchor.constraint(equalTo: view.widthAnchor).isActive = true
    label.heightAnchor.constraint(equalToConstant: 88).isActive = true
    
    if let previous = previous {
        label.topAnchor.constraint(equalTo: previous.bottomAnchor, constant: 10).isActive = true
    } else {
        label.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 0).isActive = true
    }
    
    previous = label
}
```

In this project, the “widthAnchor” is used to set the width to be as wide as the view, the “heightAchor” to be at the fixed size of 88 points, the “topAnchor” to be right at where the Safe Area is if the label is the first one at the top, otherwise have the top margin equivalent to the bottom margin of the previous label plus 10 points in between.


