## [JavaScript 表单脚本](#)
**介绍**：使用 JavaScript 既做表单验证，增强标准表单控件的默认行为。

----


----

### [1. 表单基础](#)
Web 表单在 HTML 中以 `<form>` 元素表示，在 JavaScript 中则以 HTMLFormElement 类型表示。
HTMLFormElement 类型继承自 HTMLElement 类型，因此拥有与其他 HTML 元素一样的默认属性。不
过，HTMLFormElement 也有自己的属性和方法。

- acceptCharset：服务器可以接收的字符集，等价于 HTML 的 accept-charset 属性。
- action：请求的 URL，等价于 HTML 的 action 属性。
- elements：表单中所有控件的 HTMLCollection。
- enctype：请求的编码类型，等价于 HTML 的 enctype 属性。
- length：表单中控件的数量。
- method：HTTP 请求的方法类型，通常是"get"或"post"，等价于 HTML 的 method 属性。
- name：表单的名字，等价于 HTML 的 name 属性。
- reset()：把表单字段重置为各自的默认值。
- submit()：提交表单。
- target：用于发送请求和接收响应的窗口的名字，等价于 HTML 的 target 属性。

有几种方式可以取得对 `<form>` 元素的引用。最常用的是将表单当作普通元素为它指定一个 id 属
性，从而可以使用 getElementById()来获取表单，比如：
```javascript
let form = document.getElementById("form1"); 
```
此外，使用 **document.forms** 集合可以获取页面上所有的表单元素。然后，可以进一步使用数字
索引或表单的名字（name）来访问特定的表单。比如：
```javascript
// 取得页面中的第一个表单
let firstForm = document.forms[0];
// 取得名字为"form2"的表单
let myForm = document.forms["form2"]; 
```
较早的浏览器，或者严格向后兼容的浏览器，也会把每个表单的 name 作为 document 对象的属性。
例如，名为"form2"的表单可以通过 document.form2 来访问。不推荐使用这种方法，因为容易出错，
而且这些属性将来可能会被浏览器删除。

注意，表单可以同时拥有 id 和 name，而且两者可以不相同。

#### [1.1 提交表单](#)
表单是通过用户点击提交按钮或图片按钮的方式提交的。提交按钮可以使用 type 属性为"submit"
的 `<input>` 或 `<button>` 元素来定义，图片按钮可以使用 type 属性为"image"的 `<input>`元素来定义。
点击下面例子中定义的所有按钮都可以提交它们所在的表单：

```html
<!-- 通用提交按钮 -->
<input type="submit" value="Submit Form">
<!-- 自定义提交按钮 -->
<button type="submit">Submit Form</button>
<!-- 图片按钮 -->
<input type="image" src="graphic.gif"> 
```
如果表单中有上述任何一个按钮，且焦点在表单中某个控件上，则按回车键也可以提交表单。
（textarea 控件是个例外，当焦点在它上面时，按回车键会换行。）注意，没有提交按钮的表单在按回车键时不会提交。

以这种方式提交表单会在向服务器发送请求之前触发 submit 事件。这样就提供了一个验证表单数
据的机会，可以根据验证结果决定是否真的要提交。阻止这个事件的默认行为可以取消提交表单。例如，下面的代码会阻止表单提交：
```javascript
let form = document.getElementById("myForm");

form.addEventListener("submit", (event) => {
 // 阻止表单提交
 event.preventDefault();
}); 
```
调用 preventDefault()方法可以阻止表单提交。通常，在表单数据无效以及不应该发送到服务器时可以这样处理。

当然，也可以通过编程方式在 JavaScript 中调用 submit()方法来提交表单。可以在任何时候调用
这个方法来提交表单，而且表单中不存在提交按钮也不影响表单提交。下面是一个例子：
```javascript
let form = document.getElementById("myForm");

// 提交表单
form.submit(); 
```
通过 submit()提交表单时，submit 事件不会触发。**因此在调用这个方法前要先做数据验证**。

**表单提交的一个最大的问题是可能会提交两次表单**。如果提交表单之后没有什么反应，那么没有耐
心的用户可能会多次点击提交按钮。结果是很烦人的（因为服务器要处理重复的请求），甚至可能造成
损失（如果用户正在购物，则可能会多次下单）。

解决这个问题主要有两种方式：在表单提交后禁用提交按钮，**或者通过 onsubmit 事件处理程序取消之后的表单提交**。

#### [1.2 重置表单](#)
用户单击重置按钮可以重置表单。重置按钮可以使用 type 属性为"reset"的 `<input>`或`<button>`元素来创建，比如：
```html
<!-- 通用重置按钮 -->
<input type="reset" value="Reset Form">
<!-- 自定义重置按钮 -->
<button type="reset">Reset Form</button>
```
这两种按钮都可以重置表单。表单重置后，所有表单字段都会重置回页面第一次渲染时各自拥有的
值。如果字段原来是空的，就会变成空的；如果字段有默认值，则恢复为默认值。

用户单击重置按钮重置表单会触发 reset 事件。这个事件为取消重置提供了机会。例如，以下代
码演示了如何阻止重置表单：
```javascript
let form = document.getElementById("myForm");

form.addEventListener("reset", (event) => {
 event.preventDefault();
}); 
```
与表单提交一样，重置表单也可以通过 JavaScript 调用 reset()方法来完成，如下面的例子所示：
```javascript
let form = document.getElementById("myForm");
// 重置表单
form.reset();
```
与 submit()方法的功能不同，调用 reset()方法会像单击了重置按钮一样触发 reset 事件。

#### [1.3 表单字段](#)
表单元素可以像页面中的其他元素一样使用原生 DOM 方法来访问。此外，**所有表单元素都是表单elements 属性（元素集合）中包含的一个值**。**这个 elements 集合是一个有序列表**，包含对表单中所
有字段的引用，包括所有 `<input>`、`<textarea>`、`<button>`、`<select>`和`<fieldset>` 元素。elements
集合中的每个字段都以它们在 HTML 标记中出现的次序保存，可以通过索引位置和 name 属性来访问。 以下是几个例子：

```javascript
let form = document.getElementById("form1");
// 取得表单中的第一个字段
let field1 = form.elements[0];
// 取得表单中名为"textbox1"的字段
let field2 = form.elements["textbox1"];
// 取得字段的数量
let fieldCount = form.elements.length; 
```
如果多个表单控件使用了同一个 name，比如像单选按钮那样，则会返回包含所有同名元素的
HTMLCollection。比如，来看下面的 HTML 代码片段：
```html
<form method="post" id="myForm">
 <ul>
     <li><input type="radio" name="color" value="red">Red</li>
     <li><input type="radio" name="color" value="green">Green</li>
     <li><input type="radio" name="color" value="blue">Blue</li>
 </ul>
</form> 
```
这个 HTML 中的表单有 3 个单选按钮的 name 是"color"，这个名字把它们联系在了一起。在访问
`elements["color"]` 时，返回的 NodeList 就包含这 3 个元素。而在访问 elements[0]时，只会返回
第一个元素。比如：

```javascript
let form = document.getElementById("myForm");
let colorFields = form.elements["color"];

console.log(colorFields.length); // 3
let firstColorField = colorFields[0];
let firstFormField = form.elements[0];
console.log(firstColorField === firstFormField); // true 
```
以上代码表明，使用 form.elements[0]获取的表单的第一个字段就是 form.elements["color"]中包含的第一个元素。

#### [1.4 表单字段的公共属性](#)
除 `<fieldset>` 元素以外，所有表单字段都有一组同样的属性。由于 `<input>` 类型可以表示多种表
单字段，因此某些属性只适用于特定类型的字段。除此之外的属性可以在任何表单字段上使用。以下列出了这些表单字段的公共属性和方法。

- disabled：布尔值，表示表单字段是否禁用。
- form：指针，指向表单字段所属的表单。这个属性是只读的。
- name：字符串，这个字段的名字。
- readOnly：布尔值，表示这个字段是否只读。
- tabIndex：数值，表示这个字段在按 Tab 键时的切换顺序。
- type：字符串，表示字段类型，如"checkbox"、"radio"等。
- value：要提交给服务器的字段值。对文件输入字段来说，这个属性是只读的，仅包含计算机上某个文件的路径。

这里面除了 form 属性以外，JavaScript 可以动态修改任何属性。来看下面的例子：
```javascript
let form = document.getElementById("myForm");
let field = form.elements[0];

// 修改字段的值
field.value = "Another value";
// 检查字段所属的表单
console.log(field.form === form); // true
// 给字段设置焦点
field.focus();
// 禁用字段
field.disabled = true;
// 改变字段的类型（不推荐，但对<input>来说是可能的）
field.type = "checkbox"; 
```
这种动态修改表单字段属性的能力为任何时候以任何方式修改表单提供了方便。举个例子，Web 表单的一个常见问题是用户常常会点击
两次提交按钮。在涉及信用卡扣款的情况下，这是个严重的问题，可能会导致重复扣款。对此，常见的解决方案是第一次点击之后禁用
提交按钮。可以通过监听 submit事件来实现。

```javascript
// 避免多次提交表单的代码
let form = document.getElementById("myForm");

form.addEventListener("submit", (event) => {
    let target = event.target;  
    // 取得提交按钮   
    let btn = target.elements["submit-btn"];
    // 禁用提交按钮
    btn.disabled = true;
}); 
```
以上代码在表单的 submit 事件上注册了一个事件处理程序。当 submit 事件触发时，代码会取得
提交按钮，然后将其 disabled 属性设置为 true。注意，这个功能不能通过直接给提交按钮添加
onclick 事件处理程序来实现，原因是不同浏览器触发事件的时机不一样。有些浏览器会在触发表单
的 submit 事件前先触发提交按钮的 click 事件，有些浏览器则会后触发 click 事件。对于先触发
click 事件的浏览器，这个按钮会在表单提交前被禁用，这意味着表单就不会被提交了。因此最好使用
表单的 submit 事件来禁用提交按钮。但这种方式不适用于没有使用提交按钮的表单提交。

如前所述，只有提交按钮才能触发 submit 事件。

type 属性可以用于除`<fieldset>`之外的任何表单字段。对于 `<input>` 元素，这个值等于 HTML的 type 属性值。对于其他元素，这个 type 属性的值按照下表设置。

|描 述| 示例|  HTML 类型的值        |
|:----|:----|:------------------|
|单选列表| `<select>...</select>` | "select-one"      |
|多选列表| `<select multiple>...</select>`| "select-multiple" |
|自定义按钮| `<button>...</button>`| "submit"          |
|自定义非提交按钮| `<button type="button">...</button>`| "button"          |
|自定义重置按钮| `<button type="reset">...</button>` | "reset"           |
|自定义提交按钮| `<button type="submit">...</button>`| "submit"          |

对于 `<input>` 和 `<button>` 元素，可以动态修改其 type 属性。但 `<select>` 元素的 type 属性是只读的。

#### [1.5 表单字段的公共方法](#)
每个表单字段都有两个公共方法：**focus**()和 **blur**()。focus()方法把浏览器焦点设置到表单字段，这意味着该字段会变成活动字段并可以响应键盘事件。

```javascript
window.addEventListener("load", (event) => {
    document.forms[0].elements[0].focus();
}); 
```
注意，如果表单中第一个字段是 type 为"hidden"的 `<input>` 元素，或者该字段被 CSS 属性
display 或 visibility 隐藏了，以上代码就会出错。

HTML5 为表单字段增加了 autofocus 属性，支持的浏览器会自动为带有该属性的元素设置焦点，
而无须使用 JavaScript。比如：
```javascript
<input type="text" autofocus> 
```
为了让之前的代码在使用 autofocus 时也能正常工作，必须先检测元素上是否设置了该属性。如
果设置了 autofocus，就不再调用 focus()：
```javascript
window.addEventListener("load", (event) => {
 let element = document.forms[0].elements[0];
 if (element.autofocus !== true) {
     element.focus();
     console.log("JS focus");
 }
}); 
```
因为 autofocus 是布尔值属性，所以在支持的浏览器中通过 JavaScript访问表单字段的 autofocus 属性会返回 true（在不支持的浏览器中是空字符串）。上面的代码只会在 autofocus 属性不等于 true 时调用 focus()方法，以确保向前兼容。

> 默认情况下只能给表单元素设置焦点。不过，通过将 tabIndex 属性设置为–1 再调用 focus()，也可以给任意元素设置焦点。只有 Opera 不支持这个技术。

focus()的反向操作是 blur()，其用于从元素上移除焦点。调用 blur()时，焦点不会转移到任何
特定元素，仅仅只是从调用这个方法的元素上移除了。在浏览器支持 readonly 属性之前，Web 开发者
通常会使用这个方法创建只读字段。现在很少有用例需要调用 blur()，不过如果需要是可以用的。下面是一个例子：

```javascript
document.forms[0].elements[0].blur(); 
```

#### [1.6  表单字段的公共事件](#)
除了鼠标、键盘、变化和 HTML 事件外，所有字段还支持以下 3 个事件。
- blur：在字段失去焦点时触发。
- change：在 `<input>`和`<textarea>`元素的 value 发生变化且失去焦点时触发，或者在 `<select>` 元素中选中项发生变化时触发。
- focus：在字段获得焦点时触发。

blur 和 focus 事件会因为用户手动改变字段焦点或者调用 blur()或 focus()方法而触发。这两
个事件对所有表单都会一视同仁。change 事件则不然，它会因控件不同而在不同时机触发。对于
`<input>`和`<textarea>`元素，change 事件会在字段失去焦点，同时 value 自控件获得焦点后发生变
化时触发。对于`<select>`元素，change 事件会在用户改变了选中项时触发，不需要控件失去焦点。

focus 和 blur 事件通常用于以某种方式改变用户界面，以提供可见的提示或额外功能（例如在文
本框下面显示下拉菜单）。change 事件通常用于验证用户在字段中输入的内容。比如，有的文本框可能
只限于接收数值。focus 事件可以用来改变控件的背景颜色以便更清楚地表明当前字段获得了焦点。
blur 事件可以用于去掉这个背景颜色。而 change 事件可以用于在用户输入了非数值时把背景改为红
色。以下代码展示了上述操作：
```javascript
let textbox = document.forms[0].elements[0];
textbox.addEventListener("focus", (event) => {
 let target = event.target;
 if (target.style.backgroundColor != "red") {
     target.style.backgroundColor = "yellow";
 }
});

textbox.addEventListener("blur", (event) => {
 let target = event.target;
 target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
});

textbox.addEventListener("change", (event) => {
 let target = event.target;
 target.style.backgroundColor = /[^\d]/.test(target.value) ? "red" : "";
}); 
```

这里的 onfocus 事件处理程序会把文本框的背景改为黄色，更清楚地表明它是当前活动字段。
onblur 和 onchange 事件处理程序会在发现非数值字符时把背景改为红色。为测试非数值字符，这里
使用了一个简单的正则表达式来检测文本框的 value。这个功能必须同时在 onblur 和 onchange 事件
处理程序上实现，以确保无论文本框是否改变都能执行验证。

> blur 和 change 事件的关系并没有明确定义。在某些浏览器中，blur 事件会先于
> change 事件触发；在其他浏览器中，触发顺序则相反。因此不能依赖这两个事件触发的
> 顺序，必须区分时要多加注意。

### [2. 文本框编程](#)
在 HTML 中有两种表示文本框的方式：单行使用 `<input>`元素，多行使用`<textarea>`元素。这两
个控件非常相似，大多数时候行为也一样。不过，它们也有非常重要的区别。

默认情况下，`<input>` 元素显示为文本框，省略 type 属性会以"text"作为默认值。然后可以通过size 属性指定文本框的宽度，
这个宽度是以字符数来计量的。而 value 属性用于指定文本框的初始值，maxLength 属性用于指定文本框允许的最多字符数。
因此要创建一个一次可显示 25 个字符，但最多允许显示 50 个字符的文本框，可以这样写：

```html
<input type="text" size="25" maxlength="50" value="initial value"> 
```
`<textarea>`元素总是会创建多行文本框。可以使用 rows 属性指定这个文本框的高度，以字符数
计量；以 cols 属性指定以字符数计量的文本框宽度，类似于 `<input>` 元素的 size 属性。与 `<input>` 不同的是，`<textarea>`的初始值必须包含在 `<textarea>和</textarea>` 之间，如下所示：
```html
<textarea rows="25" cols="5">initial value</textarea>
```
同样与 `<input>` 元素不同的是，`<textarea>`不能在 HTML 中指定最大允许的字符数。
除了标记中的不同，这两种类型的文本框都会在 value 属性中保存自己的内容。通过这个属性，
可以读取也可以设置文本模式的值，如下所示：
```javascript
let textbox = document.forms[0].elements["textbox1"];
console.log(textbox.value);
textbox.value = "Some new value";
```
应该使用 value 属性，而不是标准 DOM 方法读写文本框的值。比如，不要使用 setAttribute()
设置 `<input>` 元素 value 属性的值，也不要尝试修改 `<textarea>` 元素的第一个子节点。
对 value 属性的修改也不会总体现在 DOM 中，因此在处理文本框值的时候最好不要使用 DOM 方法。

#### [2.1 选择文本](#)
两种文本框都支持一个名为 select()的方法，此方法用于全部选中文本框中的文本。大多数浏览
器会在调用 select()方法后自动将焦点设置到文本框（Opera 例外）。这个方法不接收参数，可以在任
何时候调用。下面来看一个例子：

```javascript
let textbox = document.forms[0].elements["textbox1"];
textbox.select(); 
```
在文本框获得焦点时选中所有文本是非常常见的，特别是在文本框有默认值的情况下。这样做的出
发点是让用户能够一次性删除所有默认内容。可以通过以下代码来实现：
```javascript
textbox.addEventListener("focus", (event) => {
    event.target.select();
}); 
```
把以上代码应用到文本框之后，只要文本框一获得焦点就会自动选中其中的所有文本。这样可以极大提升表单易用性。

#### [2.2 select 事件](#)
与 select()方法相对，还有一个 select 事件。当选中文本框中的文本时，会触发 select 事件, 这个事件确切的触发时机因浏览器而异。

```javascript
let textbox = document.forms[0].elements["textbox1"];

textbox.addEventListener("select", (event) => {
    console.log(`Text selected: ${textbox.value}`);
});
```

#### [2.3 取得选中文本](#)
虽然 select 事件能够表明有文本被选中，但不能提供选中了哪些文本的信息。HTML5对此进行了扩展，以方便更好地获取选中的文本。

扩展为文本框添加了两个属性：selectionStart 和 selectionEnd。

这两个属性包含基于 0 的数值，分别表示文本选区的起点和终点（文本选区起点的偏移量和文本选区终点的偏移量）。因此，要取得文本框中选中的文本，可以使用以下代码：

```javascript
function getSelectedText(textbox){
    return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
} 
```
因为 **substring**() 方法是基于字符串偏移量的，所以直接传入 **selectionStart** 和 **selectionEnd** 就可以取得选中的文本。

> 这个扩展在 IE9+、Firefox、Safari、Chrome 和 Opera 中都可以使用。IE8 及更早版本不支持这两个属性，因此需要使用其他方式。

老版本 IE 中有一个包含整个文档中文本选择信息的 document.selection 对象。这意味着无法确
定选中的文本在页面中的什么位置。不过，在与 select 事件一起使用时，可以确定是触发这个事件文
本框中选中的文本。为取得这些选中的文本，必须先创建一个范围，然后再从中提取文本，如下所示：
```javascript
function getSelectedText(textbox){
    if (typeof textbox.selectionStart == "number"){
        return textbox.value.substring(textbox.selectionStart, textbox.selectionEnd);
    } else if (document.selection){
        return document.selection.createRange().text;
    }
} 
```
这个修改后的函数兼容在 IE 老版本中取得选中文本。注意 document.selection 是根本不需要textbox 参数的。

#### [2.4 部分选中文本](#)
HTML5 也为在文本框中选择部分文本提供了额外支持。现在，除了 select()方法之外，Firefox
最早实现的 setSelectionRange()方法也可以在所有文本框中使用。这个方法接收两个参数：要选择
的第一个字符的索引和停止选择的字符的索引（与字符串的 substring()方法一样）。下面是几个例子：

```javascript
textbox.value = "Hello world!"
// 选择所有文本
textbox.setSelectionRange(0, textbox.value.length); // "Hello world!"
// 选择前 3 个字符
textbox.setSelectionRange(0, 3); // "Hel"
// 选择第 4~6 个字符
textbox.setSelectionRange(4, 7); // "o w"
```
如果想看到选择，则必须在调用 setSelectionRange()之前或之后给文本框设置焦点。这个方法在IE9、Firefox、Safari、Chrome 和 Opera 中都可以使用。

IE8 及更早版本支持通过范围部分选中文本。这也就是说，要选择文本框中的部分文本，必须先使用 IE 在文本框上提供的
**createTextRange**()方法创建一个范围，并使用 **moveStart**()和 **moveEnd**()范围方法把这个范围放到正确的位置上。

```javascript
textbox.value = "Hello world!";
let range = textbox.createTextRange();

// 选择所有文本
range.collapse(true);
range.moveStart("character", 0);
range.moveEnd("character", textbox.value.length); // "Hello world!"
range.select();

// 选择前 3 个字符
range.collapse(true);
range.moveStart("character", 0);
range.moveEnd("character", 3);
range.select(); // "Hel"

// 选择第 4~6 个字符
range.collapse(true);
range.moveStart("character", 4);
range.moveEnd("character", 6);
range.select(); // "o w" 
```
与其他浏览器一样，如果想要看到选中的效果，则必须让文本框获得焦点，部分选中文本对自动完成建议项等高级文本输入框是很有用的。












