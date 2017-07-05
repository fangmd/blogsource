---
title: UITableView 使用 AutoLayout 实现 Cell 点击展开效果
date: 2017-07-02 13:18:12
tags: [ios, UITableView]
category: ios

---

![效果图](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ios/UITableViewCellExpand.gif)

接上篇文章 [UITableView Cell 高度自动匹配](http://www.jianshu.com/p/a93d23a50771) 

# 关键代码

使用改变 Label 的 numberOfLines 来实现点击扩展效果

```
mLabelContent.numberOfLines = 0  // 不限制行数，扩展
mLabelContent.numberOfLines = 2  // 限制函数为 2 行
```

更新 UITableView 的一个 Cell:

```
mTableView.rx.itemSelected.subscribe(onNext:{ indexPath in
    
    let cell = self.mTableView.cellForRow(at: indexPath) as! Cell

    self.mTableView.beginUpdates()
    cell.mModel?.isExpand = !(cell.mModel?.isExpand ?? false) // 通过更新数据源的形式改变 cell
    self.mTableView.reloadRows(at: [indexPath]UITableViewRowAnimation.automatic)            
    self.mTableView.endUpdates()
    
}).disposed(by: mDisposeBag)
```

# 完整代码块

Cell:

```
import Foundation
import SnapKit

class Cell: UITableViewCell{
    
    static let CELL_IDENTIFIER = "Cell"
    
    
    let mLabelTitle = UILabel()
    let mLabelContent = UILabel()
    let mLabelTime = UILabel()
    
    var mModel: MessageModel?{
        didSet{            
            mLabelTitle.text = mModel?.title
            mLabelContent.text = mModel?.content
            mLabelTime.text = mModel?.time
            mLabelContent.numberOfLines = (mModel?.isExpand ?? false) ? 0 : 2
        }
    }
    
    override init(style: UITableViewCellStyle, reuseIdentifier: String?) {
        super.init(style: style, reuseIdentifier: reuseIdentifier)
        selectionStyle = .none
        
        contentView.addSubview(mLabelTitle)
        contentView.addSubview(mLabelContent)
        contentView.addSubview(mLabelTime)
        
        
        mLabelTitle.snp.makeConstraints { (make) in
            make.top.equalTo(contentView).offset(4)
            make.left.equalTo(contentView).offset(16)
        }
//        mLabelContent.numberOfLines = 0
        mLabelContent.snp.makeConstraints { (make) in
            make.top.equalTo(mLabelTitle.snp.bottom).offset(3)
            make.left.equalTo(contentView).offset(16)
            make.right.equalTo(contentView).offset(-16)
        }
        mLabelTime.snp.makeConstraints { (make) in
            make.top.equalTo(mLabelContent.snp.bottom).offset(3)
            make.bottom.equalTo(contentView).offset(-4)
            make.right.equalTo(contentView).offset(-16)
        }
        
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```


MessageModel:

```
import Foundation

class MessageModel{
    
    var title: String?
    var content: String?
    var time: String?
    
    var isExpand: Bool?
    
    init() {
        
    }
    
}
```

AutoHeightViewController:

```
lazy var mTableView: UITableView = {
    let tv = UITableView()
    tv.separatorStyle = .none
    tv.register(Cell.self, forCellReuseIdentifier: Cell.CELL_IDENTIFIER)
    tv.estimatedRowHeight = 40
    tv.rowHeight = UITableViewAutomaticDimension
    return tv
}()


mTableView.rx.itemSelected.subscribe(onNext:{ indexPath in
    
    let cell = self.mTableView.cellForRow(at: indexPath) as! Cell

    self.mTableView.beginUpdates()
    cell.mModel?.isExpand = !(cell.mModel?.isExpand ?? false) // 通过更新数据源的形式改变 cell
    self.mTableView.reloadRows(at: [indexPath]UITableViewRowAnimation.automatic)            
    self.mTableView.endUpdates()
    
}).disposed(by: mDisposeBag)
```



>初学 ios ，欢迎指教


