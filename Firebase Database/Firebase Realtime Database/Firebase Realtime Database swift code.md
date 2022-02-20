### Firebase Realtime Database swift code
```swift

// AppDelegate.swift
import UIKit
import Firebase // Firebase module

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        
        // FirebaseApp instance
        FirebaseApp.configure()
        
        return true
    }
}

// CardListViewController.swift
import UIKit
import Kingfisher
import FirebaseDatabase

class CardListViewController: UITableViewController {
    var ref: DatabaseReference! // database reference

    override func viewDidLoad() {
      super.viewDidLoad()

      // firebase realtime database code
      ref = Database.database().reference()

      /* 
        이벤트를 사용하면 이벤트 발생 시점에 존재하는 지정된 경로에서 데이터를 읽을 수 있음
        이 메서드는 리스너가 연결될 때 한 번 트리거된 후 하위 요소를 포함하여 데이터가 변경될 때마다 다시 트리거 됨
        하위 데이터를 포함하여 해당 위치의 모든 데이터를 포함하는 snapshot이 이벤트 콜백에 전달
        데이터가 없으면 스냅샷은 exists()를 호출할 때 false를 반환하고 value 속성을 읽을 때 nil을 반환
      */
      ref.observe(.value) { sanpshot in
          guard let value = sanpshot.value as? [String : [String: Any]] else { return }

          do {
              let jsonData = try JSONSerialization.data(withJSONObject: value)
              let cardData = try JSONDecoder().decode([String: CreditCard].self, from: jsonData)
              let cardList = Array(cardData.values)
              self.creditCardList = cardList.sorted { $0.rank < $1.rank }

              // UI와 관련된 작업은 main thread에서 처리하므로 아래 코드에서 UI를 reload
              DispatchQueue.main.async {
                  self.tableView.reloadData()
              }
          } catch let error {
              print("error \(error)")
          }
      }     
  }
  
 // tableView를 선택했을때 호출되는 메서드
 override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    let cardID = creditCardList[indexPath.row].id
    
    /*
      setValue를 사용하면 지정된 위치에서 하위 노드를 포함하여 모든 데이터를 덮어 씀
      전체 객체를 다시 쓰지않고 하위 항목만을 업데이트 하려면 밑의 코드 처럼 쓰면 됨
    */
    // key값이 분명 할 경우
    ref.child("Item\(cardID)/isSelected").setValue(true)


    // key값이 분명하지 않을 경우
    // queryOrdered id를 기준으로 정렬하는 메서드. 정렬 메서드는 한번에 하나만 사용 가능
    // queryEqual 특정 값을 갖는 data 찾기
    ref.queryOrdered(byChild: "id").queryEqual(toValue: cardID).observe(.value) { [weak self] snapshot in
        guard let self = self,
        let value = snapshot.value as? [String: [String: Any]],
        let key = value.keys.first else { return }

        self.ref.child("\(key)/isSelected").setValue(true)
    }
  } 
 
  // swipe delete
  override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
      return true
  }

  override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
      let cardId = creditCardList[indexPath.row].id
      
       // firebase realtime database
       // key값이 분명 할 경우
       // data delete 메서드
       ref.child("Item\(cardId)").removeValue()

       // key값이 분명하지 않을 경우
       ref.queryOrdered(byChild: "id").queryEqual(toValue: cardId).observe(.value) { [weak self] snapshot in
          guard let self = self,
          let value = snapshot.value as? [String: [String: Any]],
          let key = value.keys.first else { return }

          self.ref.child(key).removeValue()
       }
     }
   }
}
```
