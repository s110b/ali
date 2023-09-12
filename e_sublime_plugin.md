```python
import sublime, sublime_plugin
import re
class Myplugin(sublime_plugin.TextCommand):
    def run(self, edit):
        bashRegion = self.view.sel()[0]
        self.view.replace(edit, bashRegion, self.view.substr(bashRegion).replace("\n", "',\n"))
        for selectedRegion in self.view.sel():
            selectedLines = self.view.lines(selectedRegion)
            adjustBy = 0
            for line in selectedLines:
                insertPoint = line.begin() + adjustBy
                self.view.insert(edit, insertPoint, "'")
                adjustBy += 1
```