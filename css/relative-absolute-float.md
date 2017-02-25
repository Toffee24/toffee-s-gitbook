1.position：relative和position：absolute都可以激活left、top、right、bottom和z-index属性（默认情况是不激活的，设置了也是无效的），在默认情况下，所有元素都是在z-index：0这一层，这就是所谓的**文档流**。

当设置relative和absolute时，元素其实会浮起来，即z-index大于0，不同的是relative会保留自己在z-index：0中的占位；而absolute会**完全脱离文档流**。absolute的left、top之类的属性是**相对于自己最近的一个设置了relative或者absolute的祖先元素，如果找不到，则相对于body**。

2.float也是可以改变文档流的，但是它不会让元素浮起到另外一个z-index层，**它让元素仍然保持在z-index：0层**，它是无法通过left、right属性来准确控制元素坐标的，而是通过float：left或者float：right控制元素在同层的左浮动或者右浮动。**同时，float会改变正常的文档流排列，影响到周围元素。**

3.还有一个很明显的区别：**absolute和float会隐式改变元素的display属性类型，无论什么类型的元素（除了display：none），只要设置了position：absolute或者float：left，元素都会以display：inline-block的方式显示（即可以设置长宽，默认宽度不满足父元素）**，而position：relative不会改变display属性。



> 在布局的时候 使用`position: relative`之后，尽量不要用`top, left, right, bottom`等属性去指定它的位置，因为它在文档流中的位置没有被释放，很容易造成其他元素排列布局不对的问题。
>
> `position: relative`更多的是用在，它的 子元素 需要固定在 它 的哪个位置显示时用的。

**还有 父元素和子元素之间，z-index是无法对比的，同级之间的z-index才能对比，**

