---
title: UITableView 使用 AutoLayout 实现 Cell 高度自适应
date: 2017-07-01 13:18:12
tags: [ios, UITableView]
category: ios

---

使用 SnapKit 实现 AutoLayout

![效果图](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/ios/UITableViewAutoHeight.png)

# UITableView 主要代码

```
    lazy var mTableView: UITableView = {
        let tv = UITableView()
        tv.separatorStyle = .none
        tv.register(Cell.self, forCellReuseIdentifier: Cell.CELL_IDENTIFIER)
        tv.estimatedRowHeight = 40
        tv.rowHeight = UITableViewAutomaticDimension
        return tv
    }()
```

# Cell 主要代码

```
contentView.addSubview(mLabelTitle)
contentView.addSubview(mLabelContent)
contentView.addSubview(mLabelTime)

mLabelTitle.snp.makeConstraints { (make) in
    make.top.equalTo(contentView).offset(4)  // top 必须和  contentView 关联，才能把 contentView 的高度撑开
    make.left.equalTo(contentView).offset(16)
}
mLabelContent.numberOfLines = 0 // 如果不设置这个 content 就是一行，不会展开
mLabelContent.snp.makeConstraints { (make) in
    make.top.equalTo(mLabelTitle.snp.bottom).offset(3)
    make.left.equalTo(contentView).offset(16)
    make.right.equalTo(contentView).offset(-16)
}
mLabelTime.snp.makeConstraints { (make) in
    make.top.equalTo(mLabelContent.snp.bottom).offset(3)
    make.bottom.equalTo(contentView).offset(-4)  // bottom 必须和 contentView 关联，才能把 contentView 的高度撑开
    make.right.equalTo(contentView).offset(-16)
}
```
