### WebService

```swift
import Foundation

protocol Webservice {
    func authenticate(_ username: String, _ password: String) -> Bool
}

class PetAdoptionService: Webservice {
    
    func authenticate(_ username: String, _ password: String) -> Bool {
        sleep(5)
        return true
    }
    
}

class FakeAuthService: Webservice {
    func authenticate(_ username: String, _ password: String) -> Bool {
        print("FakeAuthService")
        return true 
    }
}
```

### WebServiceFactory

```swift
import Foundation

class WebserviceFactory {
    
    func create() -> Webservice {
        let environment = ProcessInfo.processInfo.environment["ENV"]
        if environment == "TEST" {
            return FakeAuthService()
        } else {
            return PetAdoptionService()
        }
    }
    
}
```

### LoginViewModel

```swift
import Foundation
import Combine

class LoginViewModel: ObservableObject {
 
    var username = ""
    var password = ""
    @Published var isAuthenticated = false 
    
    var service: Webservice
    
    init(service: Webservice) {
        self.service = service
    }
    
    func login(_ username: String, _ password: String) {
        DispatchQueue.main.async {
            self.isAuthenticated = self.service.authenticate(username, password)
        }
    }
    
}
```

### LoginView

```swift
import SwiftUI

struct LoginScreen: View {
    
    @State private var username = ""
    @State private var password = ""
    @StateObject var loginVM: LoginViewModel 
    
    var body: some View {
        
        NavigationView {
            Form {
                TextField("Username", text: $username)
                    .accessibility(identifier: "usernameTextField")
                TextField("Password", text: $password)
                    .accessibility(identifier: "passwordTextField")
                HStack {
                    Spacer()
                    Button("Login") {
                        // perform login
                        loginVM.login(username, password)
                    }
                    .accessibility(identifier: "loginButton")
                    Spacer()
                }
                if loginVM.isAuthenticated {
                    Image(systemName: "lock")
                        .accessibility(identifier: "imageLock")
                }
            }
            .navigationTitle("Login")
        }
        
    }
}
```

### App

```swift
import SwiftUI

@main
struct PetAdoptionApp: App {
    var body: some Scene {
        WindowGroup {
            let webserviceFactory = WebserviceFactory()
            LoginScreen(loginVM: LoginViewModel(service: webserviceFactory.create()))
        }
    }
}
```

### UITest

```swift
import XCTest
@testable import PetAdoption

class PetAdoptionTests: XCTestCase {

    override func setUpWithError() throws {
        // Put setup code here. This method is called before the invocation of each test method in the class.
    }

    override func tearDownWithError() throws {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
    }

    func testExample() throws {
        // This is an example of a functional test case.
        // Use XCTAssert and related functions to verify your tests produce the correct results.
    }

    func testPerformanceExample() throws {
        // This is an example of a performance test case.
        self.measure {
            // Put the code you want to measure the time of here.
        }
    }

}
```
