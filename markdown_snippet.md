# 为vs code 增加markdown 自定义代码提示



```php
文件->首选项->用户代码片段
编辑 markdown.json
文件->首选项->设置-> 增加 "editor.tabCompletion": true
```

[参考资料](https://code.visualstudio.com/docs/editor/userdefinedsnippets)

## 代码片段

```js
{
	"img":{
		"prefix": "img",
		"body": ["![${1:Image Title}](${2:Image Source}) ${0}"],
		"description": "img"
	},

	"code":{
		"prefix": "code",
		"body": ["`${1:Inline Code Snippet}` ${0}"],
		"description": "code"
	},
	"hr":{
		"prefix": "hr",
		"body": [
			"---",
			"${0}"
		],
		"description": "hr"
	},
	"ol":{
		"prefix": "ol",
		"body": [
			"1. ${1:First Item}",
			"2. ${2:Second Item}",
			"3. ${3:Third Item}",
			"${0}"
		],
		"description": "ol"
	},
	"pre":{
		"prefix": "pre",
		"body": [
			"```${1:language}",
			"${2:Multi-line Code}",
			"```",
			"${0}"
		],
		"description": "pre"
	},
	"strike":{
		"prefix": "strike",
		"body": [
			"~~${1:Strike Through}~~ ${0}"
		],
		"description": "strike"
	},
	"table":{
		"prefix": "table",
		"body": [
			"| ${1:Column 1} | ${2:Column 2} |",
			"| ------------- | ------------- |",
			"| ${3:Cell 1-1} | ${4:Cell 1-2} |",
			"| ${5:Cell 2-1} | ${6:Cell 2-2} |",
			"${0}"
		],
		"description": "table"
	},
	"ul":{
		"prefix": "ul",
		"body": [
			"- ${1:I} ",
			"- ${2:Love}",
			"- ${3:Markdown}",
			"${0}"
		],
		"description": "ul"
	},
	"bq":{
		"prefix": "bq",
		"body": [
			"> ${1:Put a nice, beautiful}",
            "> ${2:quote here...}",
            "${0}"
		],
		"description": "bq"
	},
	"a":{
		"prefix": "a",
		"body": [
			"[${1:Link Title}](${2:Link Source}) ${0}"
		],
		"description": "a"
	},

	"h1":{
		"prefix": "h1",
		"body": [
			"# ${1:Heading 1}",
			"${0}"
		],
		"description": "h1"
	},
	"h2":{
		"prefix": "h2",
		"body": [
			"## ${2:Heading 2}",
			"${0}"
		],
		"description": "h2"
	},
	"h3":{
		"prefix": "h3",
		"body": [
			"### ${3:Heading 3}",
			"${0}"
		],
		"description": "h3"
	}
}```
