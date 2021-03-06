# 2017-10-17 Android学习笔记(1)TextView文本框使用 SpannableString&SpannableStringBuilder定制文本

>使用SpannableString和SpannableStringBuilder来完成
>
>两者区别：前者针对的是不可变文本，而后者则是针对可变文本

举例：
```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TextView t1 = (TextView) findViewById(R.id.txtOne);
        TextView t2 = (TextView) findViewById(R.id.txtTwo);

        SpannableString span = new SpannableString("红色打电话斜体删除线绿色下划线图片:.");
        //setSpan时需要指定的flag,Spanned.SPAN_EXCLUSIVE_EXCLUSIVE(前后都不包括)
        //1.设置背景色,
        span.setSpan(new ForegroundColorSpan(Color.RED), 0, 2, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //2.用超链接标记文本
        span.setSpan(new URLSpan("tel:4155551212"), 2, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //3.用样式标记文本（斜体）
        span.setSpan(new StyleSpan(Typeface.BOLD_ITALIC), 5, 7, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //4.用删除线标记文本
        span.setSpan(new StrikethroughSpan(), 7, 10, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //5.用下划线标记文本
        span.setSpan(new UnderlineSpan(), 10, 16, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //6.用颜色标记
        span.setSpan(new ForegroundColorSpan(Color.GREEN), 10, 13,Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
        //7.//获取Drawable资源
        Drawable d = getResources().getDrawable(R.drawable.icon);
        d.setBounds(0, 0, d.getIntrinsicWidth(), d.getIntrinsicHeight());
        //8.创建ImageSpan,然后用ImageSpan来替换文本
        ImageSpan imgspan = new ImageSpan(d, ImageSpan.ALIGN_BASELINE);
        span.setSpan(imgspan, 18, 19, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
        t1.setText(span);
    }
}
```

可以配置的属性如下

>BackgroundColorSpan 背景色
>
>ClickableSpan 文本可点击，有点击事件
>
>ForegroundColorSpan 文本颜色（前景色）
>
>MaskFilterSpan 修饰效果，如模糊(BlurMaskFilter)、浮雕(EmbossMaskFilter)
>
>MetricAffectingSpan 父类，一般不用
>
>RasterizerSpan 光栅效果
>
>StrikethroughSpan 删除线（中划线）
>
>SuggestionSpan 相当于占位符
>
>UnderlineSpan 下划线
>
>AbsoluteSizeSpan 绝对大小（文本字体）
>
>DynamicDrawableSpan 设置图片，基于文本基线或底部对齐。
>
>ImageSpan 图片
>
>RelativeSizeSpan 相对大小（文本字体）
>
>ReplacementSpan 父类，一般不用
>
>ScaleXSpan 基于x轴缩放
>
>StyleSpan 字体样式：粗体、斜体等
>
>SubscriptSpan 下标（数学公式会用到）
>
>SuperscriptSpan 上标（数学公式会用到）
>
>TextAppearanceSpan 文本外貌（包括字体、大小、样式和颜色）
>
>TypefaceSpan 文本字体
>
>URLSpan 文本超链接
>

更详细可以查看[原网页](http://www.runoob.com/w3cnote/android-tutorial-textview.html)
