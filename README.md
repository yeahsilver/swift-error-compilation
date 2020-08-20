# Swift-Error-Compilation (Korea ver. 🇰🇷) 

> 블로그와 함께 업로드 할 예정입니다!
>
> naver blog: https://blog.naver.com/0_0yeggy/222054273459



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



### **Attempt to present UIAlertController whose view is not in the window hierarchy with localnotification**

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

