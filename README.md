# Swift-Error-Compilation (Korea ver. 🇰🇷) 

> 블로그와 함께 업로드 할 예정입니다!
>
> naver blog: https://blog.naver.com/0_0yeggy



</br>


-----

</br>


### Failed to launch "project name"

: Product -> Test 돌렸더니 해결



</br>



-----



</br>



### Main Thread Checker: UI API called on a background thread 혹은 Exception: "Modifications to the layout engine must not be performed from a background thread after it has been accessed from the main thread."

1. Product -> Scheme -> Edit Scheme 

2. Diagnotics -> Main Thread Checker에 체크!
3. 다시 한번 run 해서 Main Tread 에러나는 곳 찾기!
4. 에러에 해당하는 기능 DispatchQueue.main.async()로 감싸주기



##### 예시)

```swift
public func startCount(){
        DispatchQueue.main.async {
            self.countLabel.text = "\(self.count)번"
            self.count = self.count + 1
        }
    }
```

</br>



----



</br>



### Attempt to present UIAlertController whose view is not in the window hierarchy with localnotification

- ##### 오류가 생성된 이유

  : 로그인 창에서 로그인 실패 시 UIAlertController 띄우고, 로그인을 성공할 시 main 페이지로 이동하려다가 오류 생성

- ##### 오류가 발생한 이유

  : 로그인 버튼을 누를 때, 화면 이동을 먼저하고 UIAlertController를 띄워서

- ##### 오류 해결 방법

  : 기능을 함수로 분리한 뒤, 각각에 맞는 함수 호출 (이때 main thread 에러를 방지하기 위해 DispatchQueue.main.async로 기능을 감싸주어야함.)

```swift
// 코드 일부 발췌
if myResponse.statusCode == 200 {
   let user = try JSONDecoder().decode(LoginInfo.self, from: data)
   print(user.userInfo.id)
                        
   self.idText = user.userInfo.id
   self.nameText = user.userInfo.name
   self.majorText = user.userInfo.major
   self.moveToMain()                        
} else if myResponse.statusCode == 404 || myResponse.statusCode == 500 {
          self.alert()
}

func moveToMain(){
        DispatchQueue.main.async {
            let vc = self.storyboard?.instantiateViewController(identifier: "main") as! MainViewController
                          
            vc.modalPresentationStyle = .fullScreen
            vc.idText = self.idText
            vc.nameText = self.nameText
            vc.majorText = self.majorText
                          
            self.present(vc, animated: true)
        }
    }

 func alert(){
         DispatchQueue.main.async {
            let alert = UIAlertController(title: "경고", message: "아이디 또는 비밀번호를 잘못 입력하셨습니다.", preferredStyle: .alert)
            alert.addAction(UIAlertAction(title: "확인", style: .cancel, handler: nil))
            self.present(alert, animated: true, completion: nil)
        }
    }
```



###### moveToMain() 기능을 함수에 집어넣으니 main 페이지에 데이터도 전달이 잘 되었다! ( 이전에는 안됐음 )



</br>



-----



###  typeMismatch(Swift.Array<Any>, Swift.DecodingError.Context(codingPath: [], debugDescription: "Expected to decode Array<Any> but found a dictionary instead.", underlyingError: nil))

==> type 오류



##### 변경 전

```swift
let exam = try JSONDecoder().decode([ExamInfo].self, from: data)
```

##### 변경 후

```swift
let exam = try JSONDecoder().decode(ExamInfo.self, from: data)
```



</br>



-----



</br>



### No value associated with key CodingKeys(stringValue: \"owner`\", intValue: nil)
=> ?을 생략해서 발생하는 문제였다....

##### 변경 전

```swift
struct ExamInfo: Codable{
    let owner: OwnerInfo
    let title: String
    let courseName: String
    let courseCode: String
    let startTime: String
    let endTime: String
}
```



##### 변경 후

```swift
struct ExamInfo: Codable{
    let owner: OwnerInfo?
    let title: String?
    let courseName: String?
    let courseCode: String?
    let startTime: String?
    let endTime: String?
}
```



</br>



----



</br>



### cell.detailTextViewLabel이 보이지 않을 때

=> cell의 스타일을 변경하라

##### 변경 전

```swift
let cell = UITableViewCell(style: .default, reuseIdentifier: nil)
```



##### 변경 후

```swift
let cell = UITableViewCell(style: .subtitle, reuseIdentifier: nil)
```



</br>



-----



</br>

### json으로 데이터는 잘 들어왔는데 table view가 실행되지 않는경우

=> complete() 추가! 

```swift
DispatchQueue.main.async {  completed() }
```



</br>



---



</br>



### class has no initializers

==> 변수 타입 뒤에 '!' 붙혔더니 해결



</br>



----



</br>



### 'NSInvalidArgumentException', reason: 'Invalid type in JSON write(__SwiftValue)'

=> 첫번째 코드를 두번째 코드로 변경

##### 변경 전

```swift
guard let httpBody = try? JSONSerialization.data(withJSONObject: parameter, options: []) else { return }
```



##### 변경 후

```swift
let httpBody = try! JSONEncoder().encode(parameter)
```

</br>

-----

</br>

### xcconfig unable to open file (in target in project )
1. pod deintegrate 입력

2. sudo gem install cocoapods-clean 입력

3. pod clean 입력

4. pod setup 입력

5. pod install 입력

​

후에 자신의 프로젝트 이름.xcworkspace를 open 후 command + b로 프로젝트 빌드

</br>

-----

</br>


### Command CompileSwift failed with a nonzero exit code

=> shift + command + k 입력시 해결

</br>

-----

</br>

### this class is not key value coding-compliant for the key ___."
=> View Controller에 Class 이름 확인 

</br>

-----

</br>

### My structure does not conform to protocol 'Decodable' / 'Encodable'
> https://stackoverflow.com/questions/53252019/my-structure-does-not-conform-to-protocol-decodable-encodable/53252126

</br>

----

</br>

### TMS-90683: Missing Purpose String in Info.plist
1. 아래의 링크와 같이 Privacy 추가하기
> https://kka7.tistory.com/40

해당 작업을 진행하였는데도 안되면?


2. 수동으로 입력하기

plist.info 를 source code로 연 다음, 수동으로 추가하자


먼저 plist.info에 오른쪽 마우스 버튼을 클릭한 뒤, OpenAs 에서  Source Code를 선택한다. 그 후 

    <key>NSCalendarsUsageDescription</key>
	<string>This app need to access your calendar events</string>
	<key>NSCameraUsageDescription</key>
	<string>This app need to access your camera events</string>
	<key>NSPhotoLibraryUsageDescription</key>
	<string>This app need to access your photo library events</string>

이렇게 수동으로 추가해주자.


------

### 5 duplicate symbols for architecture x86_64

Project -> Build Setting -> Other Linker Flag ->  "$(OTHER_LDFLAGS) -ObjC" 삽입
