# CSS-and-JS-compatibility-issues-summary

CSS和JS兼容性问题总结

## 1. CSS兼容性问题
* z-index兼容性问题

        假如页面中A和B是兄弟元素，C是A的子元素，D是B的子元素，当我给C和D都设置了绝对定位(position:absolute)，我给C设置了z-index:10; 给D设置了z-index:11; 此时发现在除了IE6、IE7以外的浏览器中正常显示（D元素在C元素的上层，盖住了C元素），但是在IE6和IE7中却没有达到理想状态，所以想要兼容IE6和IE7的话，解决办法如下：

给C元素和D元素的父元素，也就是A元素和B元素也设置定位（可以根据实际需求设置相对定位或者绝对定位），并且让A元素的z-index值小于B元素（例如：给A设置z-index:1; 给B设置z-index:2;），此时会发现C元素和D元素的显示效果在IE6和IE7中也达到了理想效果。

总结：如果想让一个元素（D）绝对定位置于另一个元素（C）的上层，不光要把D元素的z-index值设置的大于C元素，还要把D元素的父元素的z-index值大于C元素的父元素（注：不一定是父元素，也可以是祖先元素，但要保证两个祖先元素是兄弟元素）。

------

## 2. JS兼容性问题
* 判断数据类型

## 3. 移动端兼容性问题（H5兼容性问题）
* 父元素使用了transform，子元素position:fixed失效的问题

我的页面中有个这样的需求，点击定位，页面从右向左拉出一个菜单，覆盖整个屏幕，这个滑动效果我是用transform写的，可以选择省，但是上面当前位置和取消所占的一行要求position:fixed，下面的省可以滚动，效果如下图：

![](https://github.com/FloweringVivian/CSS-and-JS-compatibility-issues-summary/blob/master/images/img1.jpg)  

结果就是position:fixed失效了，因为他的父元素运用了transform，我的解决办法就是改变布局，让需要position:fixed的元素变成transform元素的兄弟元素，而不是子元素，这样就解决了，如果有更好的解决办法，欢迎大家踊跃发言告知，在此感谢！

------

* 移动端加了overflow:auto导致滚动不流畅的问题

不知道大家开发移动端的过程中有没有需要加overflow-x:hidden;overflow-y:auto;类似这种的情况，加了以后发现滚动变得不流畅了，这个时候只需要给你加了overflow:auto的标签（例如class="list"）加上一个神奇的css属性即可：

```javascript
.list{
    -webkit-overflow-scrolling:touch;
}
```

------
