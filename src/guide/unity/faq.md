---
title: 常见问题
type: guide_unity
order: 100
---

## UI包加载失败

```
FairyGUI: invalid package 'XXXX', 
if the files is checked out from git, 
make sure to disable autocrlf option!
```

如果你使用的FairyGUI编辑器版本小于3.1.5，那么包描述文件的格式为纯文本，当这个文件从GIT上拉下来时，有可能由于GIT的自动转换换行符功能导致文件内容发生变化从而造成FairyGUI识别错误。解决办法是关闭GIT的自动转换换行符功能，然后再重新拉一次下来。

如果你使用的FairyGUI编辑器版本大于等于3.1.5，那么包描述文件的格式已修改为二进制，不再受GIT的影响。仍然出现这个错误的原因是新的格式需要Unity SDK 1.8.3或以上版本支持。旧版本无法识别这个格式，就会报这个错误。解决办法是升级你的Unity SDK，升级你的Unity SDK，升级你的Unity SDK，重要事情说三遍。
如果暂时不想升级，可以修改xxx.fairy（你的项目描述文件），将里面的version="3.1"改成version="3"，这样编辑器就会使用旧的文本格式发布。

除了上面这个错误，如果包仍然不能加载，检查包名和路径是否正确，特别要注意包只能放置在Resources目录或者它的子目录下，否则就只能打AB包。再看看是不是Unity的项目放置在了带中文名称的目录，这都有可能造成载入失败。

## 显示白屏/提示atlas not found

```
FairyGUI: texture 'atlas0.png' not found in xxx
```

Unity5.5版本开始，纹理设置新增了[TextureShape](http://ask.fairygui.com/?/question/1)属性，把他设置为2d就可以了。 

## 字体渲染效果不对

如果你设置了字体后，觉得字体效果不对，可以用以下的方式排查：

1. 如果是只用了字体名称，没有使用ttf字体文件的，那么你需要再次确认你的操作系统（例如Windows）里是否有安装这种字体，字体名称是否正确。正确的系统字体名称可以在FairyGUI编辑器，选择任何一个文本，点字体属性旁边的A按钮看到。
2. 如果是用了ttf文件的，那么运行时在Project视图，点击ttf文件左边的箭头后，可以看到ttf文件下有Font Texture和Font Material。Font Texture里应该有你游戏中使用到的文字，并能看到渲染效果。如果能看到，说明这个字体在Unity中的渲染效果就是这样。

![](../../images/20170808230450.png)

## 层级显示错误

如果你看到了层级错乱的情况出现，那多半是因为fairyBatching的影响。在[DrawCall优化](drawcall.html)这篇教程里已经有提到，这里再说明一下。

对于打开了fairyBatching的组件，当开发者自己调用SetPosition等API改变子元件或者孙子元件的位置、大小，旋转或缩放，并不会自动触发深度调整，例如一个图片原来显示在一个窗口里的顶层，你用Tween将它从原来的位置移到另外一个位置，这个图片就有可能被窗口里的其他元素遮挡。这时开发者需要手动触发深度调整，例如：

```csharp
    aObject.InvalidateBatchingState();
```

这个API并不需要由开启了fairyBatching的组件调用，aObject可以是任何一个内含的元件。并且你可以在任何时间调用，每帧调用也可以，只要你确认是需要。它的消耗不算大，但也不能说没有。

## Stage.LateUpdate函数CPU占用过高

Stage.LateUpdate不但包含了FairyGUI自身的消耗，而且因为他是发出事件的源头，所以也包含了事件处理逻辑的消耗 。所以进入Deep Profiler模式，展开Stage.LateUpdate，查看你的事件处理函数的消耗。FairyGUI自身的消耗是在Container.Update下，在非Deep Profiler模式下是很少的（Deep Profile下的数据只能看占比值，不能看绝对值）。

## DrawCall过高

开启了FairyBatching后，仍然觉得DrawCall过高的话，可以使用Unity的工具Frame Debugger排查。可以看看是不是过多的使用了遮罩（溢出隐藏、滚动），或者元件之间不合理的重叠。另外大段文本、图片平铺、使用滤镜、BlendMode也会阻断DrawCall的合并。

## Lua不能侦听事件

```
    LuaException: Delegate FairyGUI.EventCallback1 not register
```

如果你确认已经执行了Gen Delegates，那么唯一可能的原因就是DelegateFactory没有init，请检查第三方框架的问题。

## FairyGUI支持XLua吗

无论tolua、slua还是xlua，都是C# binding的方案。FairyGUI是使用C#写的，且没有引用到除了系统库、Unity库外的任何第三方库，所以不存在不支持某某lua的说法。xlua和FairyGUI结合使用的例子可以百度“FairyGUI xlua”。


