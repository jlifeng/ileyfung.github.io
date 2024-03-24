> 编程实践中，往往连续使用多个 if 进行判断，这种代码非常冗余，也不易读，本文讨论怎么简化它。

转自：https://dreith.com/blog/theres-such-a-thing-as-using-too-many-ifs/（英文）

## 多少算太多？

有些人认为数字是一，您应该始终用至少一个三元来替换任何单个 if 语句。我不采取这种坚定的方法，但我想强调一些逃避常见 if/else 意大利面条代码的方法。

我相信很多开发人员很容易陷入 if/else 陷阱，不是因为其他解决方案的复杂性，而是因为它遵循这样的自然语言模式：

if 执行此操作， else 执行此操作。

## 等等，什么是三元？

我已经知道了，我们跳过这个吧。
三元数与 if/else 并不是革命性的区别，因为它们都是条件运算，但三元数确实返回一个值，因此可以直接在赋值中使用它。

```cpp
const greaterThanFive = (8 > 5) ? 'yep' : 'nope';

console.log(greaterThanFive); // 'yep'
```

基本模式只是一个条件，如果为真则返回一个值，如果为假则返回一个值。

```cpp
(condition) ? isTruthy : isFalsy
```

## If/Else 的替代方案

让我们从一个场景开始，逐步了解不同解决方案的示例。

我们将从用户输入中获取颜色，并需要将它们转换为一些预设的颜色代码来匹配，以便我们可以更改背景颜色。因此，我们将检查颜色名称字符串，并在匹配时设置颜色代码。

```cpp
const setBackgroundColor = (colorName) => {
	let colorCode = '';
	if(colorName === 'blue') {
		colorCode = '#2196F3';
	} else if(colorName === 'green') {
		colorCode = '#4CAF50';
	} else if(colorName === 'orange') {
		colorCode = '#FF9800';
	} else if(colorName === 'pink') {
		colorCode = '#E91E63';
	} else {
		colorCode = '#F44336';
	};
	document.body.style.backgroundColor = colorCode;
};
```

这个 if/else 就完成了工作。但是我们背负着大量重复逻辑比较 colorName 和重复赋值 colorCode 。

  

## Switch

现在我们可以更恰当地将其更改为 switch 语句。它更符合我们正在尝试做的事情的概念；我们有几种想要匹配的字符串情况，如果没有一种情况匹配，则有一个默认值。

```cpp
const setBackgroundColor = (colorName) => {
	let colorCode = '';
	switch(colorName) {
		case 'blue':
			colorCode = '#2196F3';
			break;
		case 'green':
			colorCode = '#4CAF50';
			break;
		case 'orange':
			colorCode = '#FF9800';
			break;
		case 'pink':
			colorCode = '#E91E63';
			break;
		default:
			colorCode = '#f44336';
	};
	document.body.style.backgroundColor = colorCode;
};
```

但是 switch 仍然带有大量我们可以不需要的样板文件和重复代码。

## Lookup Table查找表

那么我们真正想要实现什么目标呢？我们需要将十六进制颜色代码分配给颜色名称，因此让我们创建一个将颜色名称作为键、将颜色代码作为值的对象。然后我们可以使用 object[key] 通过颜色名称查找颜色代码。我们需要一个默认值，因此如果没有找到键，则返回默认值的短三元数将在创建对象的默认部分时执行此操作。

```javascript
const colorCodes = {
	'blue'   : '#2196F3',
	'green'  : '#4CAF50',
	'orange' : '#FF9800',
	'pink'   : '#E91E63',
	'default': '#F44336'
};

const setBackgroundColor = (colorName) => {
	document.body.style.backgroundColor = colorCodes[colorName]
		? colorCodes[colorName]
		: colorCodes['default'];
}; 
 
```

 
 
 
现在我们有了一个查找表，可以整齐地列出我们的输入和可能的输出。

这并不是奇迹般地减少了“代码行数”(LOC)（我们从 15 行减少到 20 行，再减少到 12 行）。事实上，其中一些解决方案可能会增加您的 LOC，但我们提高了可维护性、易读性，并且实际上通过仅对默认回退进行一次逻辑检查来降低复杂性。

## 数据的交易逻辑

在 if/else 或 switch 上使用查找表的最重要成就是我们将比较逻辑的多个实例转换为数据。代码更具表现力；它将逻辑显示为操作。代码更具可测试性；逻辑被减少了。而且我们的比较更容易维护；它们被合并为纯数据。

让我们将五个比较逻辑运算减少为一个，并将我们的值转换为数据。

场景：我们需要将成绩百分比转换为对应的字母成绩。

if/else 很简单；我们从上到下检查成绩是否高于或等于匹配字母成绩所需的成绩。

```javascript
const getLetterGrade = (gradeAsPercent) => {
	if(gradeAsPercent >= 90) {
		return "A";
	} else if(gradeAsPercent >= 80) {
		return "B";
	} else if(gradeAsPercent >= 70) {
		return "C";
	} else if(gradeAsPercent >= 60) {
		return "D";
	} else {
		return "F"
	};
};
```

但我们一遍又一遍地重复相同的逻辑运算。

因此，让我们将数据提取到一个数组中（以保留顺序）并将每个等级的可能性表示为一个对象。现在我们只需对对象进行一次 >= 比较，并找到数组中第一个匹配的对象。

```javascript
const gradeChart = [
	{minpercent: 90, letter: 'A'},
	{minpercent: 80, letter: 'B'},
	{minpercent: 70, letter: 'C'},
	{minpercent: 60, letter: 'D'},
	{minpercent: 0,  letter: 'F'}
];

const getLetterGrade = (gradeAsPercent) => {
	const grade = gradeChart.find(
		(grade) => gradeAsPercent >= grade.minpercent
	);

	return grade.letter;
};
```

## 开始将您的比较想象为数据

当您需要比较或“检查”值时，很自然地会使用 if/else ，这样您就可以口头逐步解决问题。但下次尝试考虑如何将您的值表示为数据，并简化您的逻辑以解释该数据。

您的代码最终将变得更具可读性、可维护性和目的性，并且其所代表的概念清晰分离。这里的所有代码示例都有效，但正确的方法可以将 It Works™ 代码变成“非常适合使用”的代码。