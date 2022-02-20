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
}
```
