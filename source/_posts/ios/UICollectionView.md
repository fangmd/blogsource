---
title: UICollectionView 使用
date: 2017-05-19 13:18:12
tags: ios
category: ios

---

# 左右滑动的时候确定当前 视图 的 index


```
mCollectionView?.delegate = self
mCollectionView?.dataSource = self

//----


extension ViewController: UICollectionViewDelegateFlowLayout, UICollectionViewDataSource, UIScrollViewDelegate{

    //...
    func collectionView(_ collectionView: UICollectionView, willDisplay cell: UICollectionViewCell, forItemAt indexPath: IndexPath) {
        let index = indexPath.row
        // index
    }
    
    
    func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
        let cells = mCollectionView?.visibleCells ?? []
        if (cells.count) > 0{
            let indexPath = mCollectionView?.indexPath(for: (cells[0]))
            let index = indexPath?.row ?? 0
            // index
        }
    }

}

```



# 隐藏滑动条

```
mCollectionView?.showsHorizontalScrollIndicator = false
mCollectionView?.showsVerticalScrollIndicator = false
```

# 设置 cell 大小充满 UICollectioinView

```
    func collectionView(_ collectionView: UICollectionView,
                        layout collectionViewLayout: UICollectionViewLayout,
                        sizeForItemAt indexPath: IndexPath) -> CGSize {
        return collectionView.frame.size
    }
```

# 监听滑动

```
    func scrollViewDidScroll(_ scrollView: UIScrollView) {
        // offset: mCollectionView?.contentOffset.y        
    }
```

<!--more-->

# 直接获取单前 cell index

```
var visibleCurrentCell: IndexPath? {
    for cell in self.collectionView.visibleCells {
        let indexPath = self.collectionView.indexPath(for: cell)
        return indexPath
     }

     return nil
}
```