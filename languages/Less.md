LESS
====

Basics
------

```
// Imports
@import “colors.less”;

// Variables:

@lightBlue:#ddf
.shape{
    background:@lightBlue;
}


// Mixins (basically a function)

.RoundedShape(@radius:@defaultRadius){
    -webkit-border-radius:@radius;
       -moz-border-radius:@radius;
            border-radius:@radius;
}
.Round{
    .RoundedShape(9999px);
}


// Arithmetic
@defaultShapesWidth:200px;
@borderSize:@defaultShapesWidth * 0.1;

@defaultThemeColor:@lightBlue;
@borderColor:@defaultThemeColor - #222;

// Color ops
darken() and lighten() - add some black or white,
saturate() and desaturate() - make a color more "colorful" or more "grayscale",
fadein() and fadeout() - increase or reduce transparency,
spin() - modifies the hue of the color.

@defaultThemeColor:@lightBlue;
@borderColor:darken(desaturate(@defaultThemeColor, 100%), 20%);
```


Nesting
-------

```
//Instead of:
#header h1 { }

// Use:
#header {
    h1 { }
}
```
