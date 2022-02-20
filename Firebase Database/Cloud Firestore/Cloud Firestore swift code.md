### Cloud Firestore swift code
```swift

//AppDelegate.swift
import UIKit
import Firebase
import FirebaseFirestoreSwift

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionkey: Any]?) -> Bool {
    FirebaseApp.configure() // FirebaseApp instance
    
    let db = Firestore.firestore() // init Cloud Firestore instance
    
    // firestore db에 creditCardList라는 collection(data)가 없으면 추가하는 코드
    db.collection("creditCardList").getDocuments { snapshot, _ in 
      guard snapshot?.isEmpty == true else { retrun }
      let batch = db.batch() // data 일괄 쓰기
      let card0Ref = db.collection("creditCardList").document("card0")
      let card1Ref = db.collection("creditCardList").document("card1")
      let card2Ref = db.collection("creditCardList").document("card2")
      let card3Ref = db.collection("creditCardList").document("card3")
      let card4Ref = db.collection("creditCardList").document("card4")
      let card5Ref = db.collection("creditCardList").document("card5")
      let card6Ref = db.collection("creditCardList").document("card6")
      let card7Ref = db.collection("creditCardList").document("card7")
      let card8Ref = db.collection("creditCardList").document("card8")
      let card9Ref = db.collection("creditCardList").document("card9")
      
      do {
        try batch.setData(from: CreditCardDummy.card0 , forDocument: card0Ref)
        try batch.setData(from: CreditCardDummy.card1 , forDocument: card1Ref)
        try batch.setData(from: CreditCardDummy.card2 , forDocument: card2Ref)
        try batch.setData(from: CreditCardDummy.card3 , forDocument: card3Ref)
        try batch.setData(from: CreditCardDummy.card4 , forDocument: card4Ref)
        try batch.setData(from: CreditCardDummy.card5 , forDocument: card5Ref)
        try batch.setData(from: CreditCardDummy.card6 , forDocument: card6Ref)
        try batch.setData(from: CreditCardDummy.card7 , forDocument: card7Ref)
        try batch.setData(from: CreditCardDummy.card8 , forDocument: card8Ref)
        try batch.setData(from: CreditCardDummy.card9 , forDocument: card9Ref)

      } catch let error {
          debugPrint("error : \(error.localizedDescription)")
      }

      // db에 data 추가
      batch.commit() { error in
        if let error = error {
          debugPrint("error : \(error.localizedDescription)")
        } else {
          debugPrint("Batch write succeeded.")
        }
      }
    }
  }
}

// CardListViewController
import UIKit
import FirebaseFirestore

class CardListViewController: UITableViewController {
  var db = Firestore.firestore()
  var creditCardList: [CreditCard] = []

  // Firestore read
  // addSnapshotListener -> 연결된 주소의 데이터가 바뀔때마다 자동으로 업데이트를 해줌
  db.collection("creditCardList").addSnapshotListener { snapshot, error in
    guard let documents = snapshot?.documents else {
      debugPrint("ERROR Firestore fetching document \(String(describing: error))")
      return
    }
    
    // db creditCardList에 nil값이 들어간 것은 swift의 creditCardList에 넣지 않기 위해서 compactMap으로 nil을 반환
    self.creditCardList = documents.compactMap { doc -> CreditCard? in
      do {
        let jsonData = try JSONSerialization.data(withJSONObject: doc.data(), options: [])
        let creditCard = try JSONDecoder().decode(CreditCard.self, from: jsonData)
      } catch let error {
        debugPrint("ERROR Json parsing \(error)")
        return nil
      }
    }.sorted { $0.rank < $1.rank }
    
    DispatchQueue.main.async {
        self.tableView.reloadData()
    }
  }
  
  override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    let cardID = creditCardList[indexPath.row].id
    //firebase firestore
    // key값이 분명 할 경우
    // updateData : 전체 문서를 덮어쓰지 않고 문서의 일부 필드를 업데이트
    db.collection("creditCardList").document("card\(cardID)").updateData(["isSelected": true])

    // key값이 분명하지 않을 경우
    // creditCardList의 id중 cardID와 같은 모든 데이터 반환 후 결과를 가져오는 메소드
    db.collection("creditCardList").whereField("id", isEqualTo: cardID).getDocuments { snapshot, _ in
        guard let document = snapshot?.documents.first else {
            debugPrint("Error Firestore fetching document")
            return
        }

        document.reference.updateData(["isSelected": true])
    }
  }
  
  override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
      return true
  }

  override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
      let cardId = creditCardList[indexPath.row].id
      
      // firebase firestore
      // key값이 분명 할 경우
      // delete : 문서 삭제
      db.collection("creditCardList").document("card\(cardId)").delete()
      
      // key값이 분명하지 않을 경우
      db.collection("creditCardList").whereField("id", isEqualTo: cardId).getDocuments { snapshot, _ in
          guard let document = snapshot?.documents.first else {
              debugPrint("Error Firestore fetching document")
              return
          }

        document.reference.delete()
      }
   }
}
```
