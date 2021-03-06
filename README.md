# AListViewController

[![CI Status](http://img.shields.io/travis/Arnoymous/AListViewController.svg?style=flat)](https://travis-ci.org/Arnoymous/AListViewController)
[![Version](https://img.shields.io/cocoapods/v/AListViewController.svg?style=flat)](http://cocoapods.org/pods/AListViewController)
[![License](https://img.shields.io/cocoapods/l/AListViewController.svg?style=flat)](http://cocoapods.org/pods/AListViewController)
[![Platform](https://img.shields.io/cocoapods/p/AListViewController.svg?style=flat)](http://cocoapods.org/pods/AListViewController)

<img src="Chat-demo.gif" width="250" height="447">
<img src="Dribble-demo.gif" width="250" height="447">

## Features
- Configure `TableView` or `CollectionView` with a **SINGLE** method
- Manage sections and rows easily and animatedly
- PullToRefresh and infiniteScrolling

## Installation

AListViewController is available through [CocoaPods](http://cocoapods.org). 

To install it, simply add the following line to your Podfile:

```ruby
pod "AListViewController"
pod "AListViewController/PullToRefresh" #pullToRefresh feature
pod 'AListViewController/InfiniteScrolling' #infiniteScrolling feature
```

## Requirements

Swift 3.0

## How it work ?

AListViewController has two subclasses :
- `ATableViewController`
- `ACollectionViewController`

The only difference between those subclasses is the `ListView`: 
- UITableView for `ATableViewController`
- UICollectionView for `ACollectionViewController`

### Usage

Call `self.configure()` before `super.viewDidLoad()` with arguments:

`cellIdentifier: @escaping (IndexPath, Any) -> String`
- Configure cell identifier at IndexPath

`cellUpdate: @escaping (IndexPath,Any,ListViewCell) -> Void`
- Update cell at IndexPath

`ATableViewController` : `cellSize: ((IndexPath, Any) -> CGSize)?`
`ACollectionViewController` : `cellHeight: ((IndexPath, Any) -> CGFloat)?`
- Set cell size for IndexPath

`ACollectionViewController` : `minimumSpacing: ((Int) -> CGSize)?`
- Set minimum spacing for Section (width: interItem, height: line)

`sourceObjects: @escaping ((@escaping ([[Any]], Bool) -> Void) -> Void)`
- Fetch data in this closure.
- Call completion closure to return these informations.
    - The fetched new objects (sourceObjects).
    - If the next loading exists (hasNext).
    
`didSelectCell: ((IndexPath,Any) -> Void)?`
- Handle press on cell

`public func refreshData(reload: Bool, immediately: Bool)`
- reload: true - replace `ListView` content with `fetchSourceObjects` 
      - immediately: true
          - Immediately makes the `ListView` empty.
      - immediately: false
          - Refreshes the tableView after fetching the data.
- reload: false - append `fetchSourceObjects` to `ListView` content  

## Example

```swift
import AListViewController

class ExampleTableViewController: ATableViewController {

    let section0 = ["Cell 1 Section 1","Cell 2 Section 1"]
    let section1 = ["Cell 1 Section 2","Cell 2 Section 2"]

    override func viewDidLoad() {
        
        self.configure(cellIdentifier: { _ -> String in
            
            return "cell"
            
        }, cellUpdate: { (_, object, cell) in
            
            cell.textLabel?.text = object as? String
            
        }, sourceObjects: { (completion) in
            
            completion([self.section0,self.section1], true)
            
        }, didSelectCell: { (_, object) in
            
            print("select: \(object as! String)")
            
        })
        super.viewDidLoad()
    }

    //You still can override UITableViewDelegate or UICollectionViewDelegate methods for additional customizations
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return "Section \(section)"
    }
}
```

In this exemple the `UITableView` is linked to your storyboard and you have a prototype cell with "cell" as identifier.
If you call `refreshData(reload: true)` the content of your `ListView` doesn't change.

But if you call `refreshData(reload: false)` :
- `self.section0` is add to section at index 0
- `self.section1` is add to section at index 1

Now, 
- section at index 0 now contains `["Cell 1 Section 1","Cell 2 Section 1","Cell 1 Section 1","Cell 2 Section 1"]`
- section at index 0 now contains `["Cell 1 Section 2","Cell 2 Section 2","Cell 1 Section 2","Cell 2 Section 2"]`

## Additional Usage
```swift

public var fetchSourceObjectsOnViewDidLoad: Bool
public var rowAnimationEnabled: Bool

public var pullToRefreshEnabled: Bool //AListViewController/PullToRefresh
public var infiniteScrollingEnabled: Bool //AListViewController/InfiniteScrolling

open func addPullToRefresh(_ animator: ESRefreshProtocol & ESRefreshAnimatorProtocol) //AListViewController/PullToRefresh
open func addInfiniteScrolling(_ animator: ESRefreshProtocol & ESRefreshAnimatorProtocol) //AListViewController/InfiniteScrolling

public var rowAnimation: (delete: UITableViewRowAnimation, insert: UITableViewRowAnimation, reload: UITableViewRowAnimation) //ATableViewController

public func registerCellClass(_ `class`:AnyClass,withIdentifier identifier: String)
public func registerCellNib(_ nib: UINib,withIdentifier identifier: String)

public func insertSection(withObject objects:[Any]...,at index: Int? = nil)
public func insertSections(withObjects objects:[[Any]],at index: Int? = nil)

public func deleteSection(withIndex indexs:Int...)
public func deleteSections(withIndexs indexs:[Int]? = nil)

public func reloadSection(withIndex indexs:Int...)
public func reloadSections(withIndexs indexs:[Int]? = nil)

public func insertRow(withObject object:Any,at indexPath:IndexPath? = nil)
public func insertRows(withObjects objects:[Any],at indexPaths:[IndexPath])

public func deleteRow(withIndex indexs: IndexPath...)
public func deleteRows(withIndexs indexs: [IndexPath])

public func reloadRow(withIndex indexs:IndexPath...)
public func reloadRows(withIndexs indexs:[IndexPath]? = nil)

public func object(atIndexPath indexPath: IndexPath) -> Any
```
## Author

Arnaud Dorgans

Twitter [@arnauddorgans](http://twitter.com/arnauddorgans)

Email <arnaud.dorgans@gmail.com>

## Acknowledgment

Inspired by [mishimay](https://github.com/istyle-inc/LoadMoreTableViewController)

## License

AListViewController is available under the MIT license. See the LICENSE file for more info.
