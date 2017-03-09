---
categories: iOS
tag:  
- color  
---
# iOS开发之像素Compositing   
假如`Layer S`·在`Layer D`上面，则最终的屏幕的颜色值如下: 
$$R = S + D \cdot (1- S_\alpha)$$
$R$: 最终的RGB  
$S$： source color，顶层的颜色，已经经过相乘  
$D$： destination color，底层的颜色，已经经过相乘  

设顶层颜色用RGB表示是$(r_s,g_s,b_s,\alpha _s)$，底层颜色用RGB表示是$(r_d, g_d, b_d, \alpha_d)$，则$$S=(r_s \cdot \alpha_s, g_s \cdot \alpha_s, g_s \cdot \alpha_s)$$$$D=(r_d \cdot \alpha_d, g_d \cdot \alpha_d, g_d \cdot \alpha_d)$$
于是$$\begin{align*}
 R &= S + D \cdot (1 - \alpha_s)  \\
 &= (r_s\cdot\alpha_s+(1-\alpha_s)\cdot(r_d\cdot\alpha_d),  f_s\cdot\alpha_s+(1-\alpha_s)\cdot(g_d\cdot\alpha_d), b_s\cdot\alpha_s+(1-\alpha_s)\cdot(b_d\cdot\alpha_d))
 \end{align*}
$$




