# Links

codepen.io
devdocs.io

# HTML

h1 -> h6

# CSS

inline > internal > external  
dont use id selector
pesudo class(:hover :active ...)

## display

**common block elements**: p h1-h6 div ol/ul/li form  
**common inline elements**: span,img,a  
cannot change width of inline element, but block element can, inline-block can support both feature.  
**display: none 和 visibility hidden 的区别？**  
前者 remove element，后者保留位置但是隐藏

## positioning

children sit on parents
**position**:static, relative, absolute, fixed  
static: by default  
relative: can move element top/bottom/left/right from where it used to be, and it does not affect other elements  
absolute: it affects the layout of other elements. right:20px means 20 px away from the right border of the parent element  
fixed: pinned on the screen even when we scroll down

## text-align

textalign needs to be set in parent element, only affect element without a width attr  
如果指定了宽度导致 center 失效怎么办？margin:0 auto 0 auto
