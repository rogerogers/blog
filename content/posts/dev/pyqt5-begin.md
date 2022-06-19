---
title: "Build your gui use pyqt5"
author: "rogers"
date: 2022-06-19T10:56:57+08:00
slug: "build-your-gui-use-pyqt5"
summary: "Build your first cross platform gui application use pyqt5"
tags: ["gui", "python", "pyqt5"]
---

## source

[anaconda download](https://mirror.tuna.tsinghua.edu.cn/anaconda/archive/)

## install

### Windows

Install anaconda

### linux && mac

```bash
#you should have python3 installed first
#mac
brew install python3
#ubuntu
apt install python3 # apt install python-is-python3 make python default version 3
#fedora
dnf install python3 # dnf install python-is-python3
#using pip or pipenv, i prefer pipenv
pipenv install pyqt5
```

> You may want to install pyqt5-tools install the qt-designer pipenv install pyqt5-tools . This makes your dev easilier

## qt-designer

### Create your first ui

![main ui](/images/qt-designer.PNG)

> You can design you ui by dragging the components, and change attributes of your components
> Press ctrl-r review your design

### Convert your ui file to python code

```bash
pyuic5 -x main.ui -o ui/main_window.py
```

### Run your code

```python
import sys

from main_window import Ui_MainWindow
from PyQt5.QtWidgets import QMainWindow,QApplication

class MainWindow(QMainWindow):
def __init__(self, parent=None) -> None:
    super().__init__(parent)
    self.ui = Ui_MainWindow()
    self.ui.setupUi(self)

def main():
    app = QApplication(sys.argv)
    window = MainWindow()
    #you can set title
    #window.setWindowTitle("hello world")
    window.show()
    #you can set icon
    #app.setWindowIcon("icon.icon")
    sys.exit(app.exec_())

if __name__ == "__main__":
    main()
```

![pyqt run](/images/pyqt-run.PNG)

pyqt is so awesome
