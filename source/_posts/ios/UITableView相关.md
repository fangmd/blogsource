---
title: UITableView 相关
date: 2017-07-12 13:18:12
tags: [ios, UITableView]
category: ios

---


# head view 设置大小

```
just set head view frame
```


# cell 中创建圆形图片

```
extension UIImageView{

    func asCircle(size: CGFloat = -1){
        if size == -1{
            layer.cornerRadius = (bounds.size.width/2)
        }else{
            layer.cornerRadius = size
        }
    }

    func loadCircleImage(url: String){
        self.layer.masksToBounds = true
        self.layer.cornerRadius = self.frame.size.width / 2.0;
        self.clipsToBounds = true
        let urlResult = URL(string: url)
        self.kf.setImage(with: urlResult, options: [.transition(.none)])
    }
    
}

```

```
    var mModel: ForumItemModel?{
        didSet{            
            let imgUrl = mModel?.creatorPhotoUrl ?? ""
            mIvAvatar.loadCircleImage(url: imgUrl)
            mIvAvatar.asCircle(size: 7)
        }
    }
```