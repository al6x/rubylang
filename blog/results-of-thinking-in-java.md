# Result of thinking in Java

I was recently involved in some discussion about Java and DSL and had an interesting
observation how the language can impact the thinking. It was so explicit that I decided to
discuss it in more details.

Please take a look at the code below:

``` java
int labelsWidth=150;
  layoutless
    .item(new ComponentBox()
      .component(jLabel1)
      .width(labelsWidth)
      .height(22)
      .x(0)
      .y(8+25*0)
      )
    .item(new ComponentBox()
      .component(jTextField1)
      .width(layoutless.width().minus(labelsWidth).minus(16).minus(50))
      .height(22)
      .x(labelsWidth+8)
      .y(8+25*0)
      )
    .item(new ComponentBox()
      .component(jButton2)
      .width(49)
      .height(21)
      .x(layoutless.width().minus(58))
      .y(8+25*0)
      )
    .item(new ComponentBox()
      .component(jLabel2)
      .width(labelsWidth)
      .height(22)
      .x(0)
      .y(8+25*1)
      )
    .item(new ComponentBox()
      .component(jTextField2)
      .width(layoutless.width().minus(labelsWidth).minus(16))
      .height(22)
      .x(labelsWidth+8)
      .y(8+25*1)
      )
    .item(new ComponentBox()
      .component(jLabel3)
      .width(labelsWidth)
      .height(22)
      .x(0)
      .y(8+25*2)
      )
    .item(new ComponentBox()
      .component(jPasswordField1)
      .width(layoutless.width().minus(labelsWidth).minus(16))
      .height(22)
      .x(labelsWidth+8)
      .y(8+25*2)
      )
    .item(new ComponentBox()
      .component(jButton1)
      .width(90)
      .height(27)
      .x(labelsWidth+8)
      .y(layoutless.height().minus(40))
      )
    .item(new ComponentBox()
      .component(iconLabel)
      .width(128)
      .height(128)
      .x(0)
      .y(layoutless.height().minus(140))
      )
    ;
  this.add(layoutless, BorderLayout.CENTER);
```

This code builds simple dialog showed on the picture below:

![](results-of-thinking-in-java/dialog.png)

Participants of those thread insisted that **this is a good sample of Java DSL** and proposed
to rewrite it in Ruby and thus prove that it is better for building DSL.

Can You rewrite this DSL in Ruby and make it better? The answer is **NO**. Why? Because good DSL
made from 2 ingredients:

1. Good design (ideally it should be language agnostic).
2. Good implementation in the target language.

The problem with this DSL is that **its design is bad** and no matter what implementation
do You choose the result will be bad also.

So, how the language impacts the thinking? The impact is:

- After a while You starting to believe that this shit is actually a DSL and You became used to
it and satisfied with it and believing that this is the right way to go.
- The limitations of language at some point places limitations to Your imagination. And You thinking
about the design staying within limitations of language and became blind to possibilities
lying outside of it.

For example - with Java Thinking You became blind to alternative designs, like this one:

``` ruby
dialog
  row 30%, 70%
    icon 'image with keys'
    form
      row
      text 'Label1'
      button '...'
      text 'Label2'
      password
  button 'ok'
```

It's better to get used to many languages. And even if You works with Java only it will free Your imagination.

So, don't think only in Java.

Tags : DSL, Design
Date : 2011/12/1