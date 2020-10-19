---
title: ios 优化
date: 2017-06-17 13:18:12
tags: ios
category: ios

---


# 懒加载

让对象在使用的时候在加载到内存中

```
lazy var 变量: 类型 = { 创建变量代码 }()
```


# 内存泄漏 

<!--more-->

## 闭包

例子：

```
class CustomCell: UITableViewCell {

    @IBOutlet weak var customButton: UIButton!
    var onButtonTap:(()->Void)?

    @IBAction func buttonTap(){
        onButtonTap?()
    }
}

---

class ViewController: UITableViewController {

    ...
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "CustomCell", for: indexPath) as! CustomCell
        cell.onButtonTap = { _ in
            self.navigationController?.pushViewController(NewViewController(), animated: true)
        }
    }        
}
```

上面的代码中：CustomCell 拥有 onButtonTap，UITableView 拥有 CustomCell，UITableViewController 拥有 UITableView，onButtonTap 拥有 UITableViewController 形成互相引用造成内存泄漏

处理办法：

```
cell.onButtonTap = { [unowned self] in
    self.navigationController?.pushViewController(NewViewController(), animated: true)
}

//or
[weak self]
```

一般来说如果闭包生命周期不长于其捕获的上下文变量的生命周期我们会使用 unowned，否则我们选择 weak 。

