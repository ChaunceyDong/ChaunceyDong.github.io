---
layout:     post
title:      Pyqtgraph Tutorial
subtitle:   
date:       2020-06-12
author:     Chauncey
header-img: img/post-bg-isos9-web.jpg
catalog: true
tags:
    - High Frequency Trading
    - Notes
    - Strategy

---



```python

## Start Qt event loop unless running in interactive mode or using pyside.
if __name__ == '__main__':
    import sys
    if sys.flags.interactive != 1 or not hasattr(QtCore, 'PYQT_VERSION'):
        pg.QtGui.QApplication.exec_()

```

