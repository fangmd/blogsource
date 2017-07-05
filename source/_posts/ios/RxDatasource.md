---
title: Rxdatasource
date: 2017-06-06 13:18:12
tags: ios
category: ios

---


# 


```
dataSource.configureCell = { (ds: RxTableViewSectionedReloadDataSource<SectionOfCustomData>, tv: UITableView, ip: IndexPath, item: Item) in
  let cell = tv.dequeueReusableCell(withIdentifier: "Cell", for: ip)
  cell.textLabel?.text = "Item \(item.anInt): \(item.aString) - \(item.aCGPoint.x):\(item.aCGPoint.y)"
  return cell
}
```

<!--more-->

# 1 没有使用 rxdatasource

```
var mDataSource = Variable.init([CourseEntity]())

mTableView.register(CourseCell.self, forCellReuseIdentifier: CourseCell.CELL_IDENTIFIER)

mVM.mDataSource.asObservable()
    .bind(to: mTableView.rx.items(cellIdentifier: CourseCell.CELL_IDENTIFIER, cellType: CourseCell.self)){
        row, model , cell in
        cell.mModel = model
    }.addDisposableTo(mDisposeBag)

```

# 2 使用 rxdatasource

```
let dataSource = RxTableViewSectionedReloadDataSource<SectionOfCustomData>()

dataSource.configureCell = { (ds: RxTableViewSectionedReloadDataSource<SectionOfCustomData>, tv: UITableView, ip: IndexPath, item: Item) in
  let cell = tv.dequeueReusableCell(withIdentifier: "Cell", for: ip)
  cell.textLabel?.text = "Item \(item.anInt): \(item.aString) - \(item.aCGPoint.x):\(item.aCGPoint.y)"
  return cell
}
dataSource.titleForHeaderInSection = { ds, index in
  return ds.sectionModels[index].header
}

```

```

var mCourse = Variable.init([SectionModel<String, CourseEntity>]())

let mCourseDataSource = RxCollectionViewSectionedReloadDataSource<SectionModel<String, CourseEntity>>()

mCourseDataSource.configureCell = {
    _, collectionView, indexPath, model in
	let cell  = collectionView.dequeueReusableCell(withReuseIdentifier: HomeCourseCell.CELL_IDENTIFIER, for: indexPath) as! HomeCourseCell
		cell.mModel = model
		return cell
}


mVM.mCourse.asObservable()
	.bind(to: (mCollectionViewRecentCourse.rx.items(dataSource: mCourseDataSource))).disposed(by: mDisposeBag)

```


# itemSelected

点击事件

```
mTableView.rx.itemSelected.subscribe(onNext:{ indexPath in
    let model = self.mVM.mDataSource.value[indexPath.row]
    self.navigationController?.pushViewController(CourseDetailVC(courseId: model.termCourseId!, courseName: model.termCourseName!), animated: true)
            
}).disposed(by: mDisposeBag)
```        





