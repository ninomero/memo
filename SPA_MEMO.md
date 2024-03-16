# 1

## 1.1 環境構築

・ターミナルコマンド  
`npm crate-react-app [ファイル名]`

### 1.1.2 Firebase作成

1.Firebaseログイン  
2.新規プロジェクト作成  
3.ウェブからアプリを作成する  
4.開発環境にて`npm install -g firebase-tools`をたたく  
5.プロジェクトの設定からリソースロケーションを設定する  
6.FireStoreDatabaseの設定→本番環境で開始を選択  

### 1.1.3 Firebaseと開発環境をコネクトする

1.`firebase login`をたたく  
2.`firebase init`をたたいて初期設定をする  
3. `Firebase``Functions``Hosting``Storage`を選択  
4. 既存のプロジェクトを選択`Use an existing project`を選択  
5. 先ほど作成したFirebaseを選択  
6. セキュリティファイルを聞かれるがデフォルトでよいので`Enter`する  
7. ファイアベースのインデックスを聞かれるのがデフォルトでよいので`Enter`する
8. Languageは好きなものを選ぶ
9. ESlingを聞かれるがいったんは`No`でよい
10. 依存関係のパッケージをインストールするか聞かれるので`Enter=Yes`する
11. どのディレクトリで使用されるか聞かれるが本番で公開するのが`build`のため、それを指定する
12. SPA設定するか聞かれるので`Yes`を答える

### 1.1.4 必要なパッケージをインストールする

npm install --save @material-ui/core @material-ui/icons @material-ui/styles connected-react-router firebase history react-redux react-router redux redux-actions redux-logger redux-thunk reselect  
※material-uiがインストールエラーになるので気を付ける  

### 1.1.5 Firebaseに公開する

1. `npm run build`
2. `firebase deploy`

## 2 認証機能

## 2.1 データ設計イメージ  

![alt text](assets/image.png)  

## 2.2 Firebase Authenticationを使用して認証機能を実装する  

### 2.2.1 ConsoleからAuthenticationを設定する  

### 2.2.2 Authenticationからログイン方法を設定  

![alt text](assets/image_02.png)

プロジェクト設定に移りConfig情報をコピーする  

![alt text](assets/image_03.png)  

config.jsファイルを作成し、ペーストする  

![alt text](assets/image_04.png)  
※exportを記載してエントリーポイントで使用できるようにしておく  

また、index.jsを以下のような構成で作成しておく  

```js
import firebase from 'firebase/app'
import 'firebase/auth'
import 'firebase/firestore'
import 'firebase/storage'
import 'firebase/functions'
import { firebaseConfig } from './config';

// firebaseの初期化処理
// config情報を使用して初期化している
firebase.initializeApp(firebaseConfig);

// 使用しやすいように変数に設定
export const auth = firebase.auth();
export const db = firebase.firestore();
export const storage = firebase.storage();
export const functions = firebase.functions();
export const FirebaseTimestamp = firebase.firestore.Timestamp;
```

### 2.2.3 TextInputコンポーネントを作成

components/UIkitフォルダ構成を作成し、TextInputを作成していく  

```js
import React from 'react'
import TextField from "@material-ui/core"

const TextInput = (props) => {
    return (
        <TextField 
            fullWidth={props.fullWidth}
            label={props.multiline}
            margin="dense"
            multiline={props.multiline}
            required={props.required}
            rows={props.rows}
            value={props.value}
            type={props.type}
            onChange={props.onChange}
        />
    )
}
export default TextInput
```

SinUp画面を作成  

templateフォルダを作成し、SinUp.jsxを作成  

```jsx
import React, {useCallback, useState} from 'react'
import {TextInput} from '../components/UIkit'

const SignUp = () => {

    const   [username, setUsername] = useState(""),
            [email, setEmail] = useState(""),
            [password, setPassword] = useState(""),
            [confirmPassword, setConfirmPassword] = useState("");

    // useCallback関数を使用して無駄なレンダリングを防いでいる
    // eventはonChangeのイベント時の値が入ってくる
    const inputUsername = useCallback((event) => {
        setUsername(event.target.value)
    }, [setUsername]);

    const inputEmail = useCallback((event) => {
        setEmail(event.target.value)
    }, [setEmail]);

    const inputPassword = useCallback((event) => {
        setPassword(event.target.value)
    }, [setPassword]);

    const inputConfirmPassword = useCallback((event) => {
        setConfirmPassword(event.target.value)
    }, [setConfirmPassword]);

    return (
        <div className='c-section-container'>
            <h2 className='u-text_headline u-text-center'>アカウント登録</h2>
            <div className="module-spacer--medium" />
            {/* TextInputコンポーネントを使用して作成していく */}
            <TextInput 
                fullwidth={true} label={"ユーザー名"} multiline={false} required={true}
                rows={1} value={username} type={"text"} onChange={inputUsername}
            />
            <TextInput 
                fullwidth={true} label={"メールアドレス"} multiline={false} required={true}
                rows={1} value={email} type={"email"} onChange={inputEmail}
            />
            <TextInput 
                fullwidth={true} label={"パスワード"} multiline={false} required={true}
                rows={1} value={password} type={"password"} onChange={inputPassword}
            />
            <TextInput 
                fullwidth={true} label={"パスワード(再確認)"} multiline={false} required={true}
                rows={1} value={confirmPassword} type={"password"} onChange={inputConfirmPassword}
            />
        </div>
    )
}

export default SignUp
```

### 2.2.4 ログインするためのボタン作成

#### 2.2.4.1 PrimaryButton.jsx作成

```jsx
import React from 'react'
import Button from '@mui/material/Button';

const PrimaryButton = (props) => {
  return (
    <Button variant='contained' onClick={() => props.onClick()}>
        {props.label}
    </Button>
  )
}

export default PrimaryButton
```

※index.jsにエントリーポイントとして記載する

### 2.2.5 redux/users/operations.jsxにsignUp関数実装

### 2.2.5.1 signUp

```jsx
export const signUp = (username, email, password, confirmPassword) => {
    return async (dispatch) => {
        // Validations
        // 入力した値が正しいかどうかを確認する
        if(!isValidRequiredInput(email, password, confirmPassword)) {
            alert('必須項目が未入力です。');
            return false
        }

        if(!isValidEmailFormat(email)) {
            alert('メールアドレスの形式が不正です。もう1度お試しください。')
            return false
        }
        if (password !== confirmPassword) {
            alert('パスワードが一致しません。もう1度お試しください。')
            return false
        }
        if (password.length < 6) {
            alert('パスワードは6文字以上で入力してください。')
            return false
        }

        return auth.createUserWithEmailAndPassword(email, password)
            .then(result => {
                dispatch(showLoadingAction("Sign up..."))
                const user = result.user;
                if (user) {
                    const uid = user.uid;
                    const timestamp = FirebaseTimestamp.now();

                    const userInitialData = {
                        customer_id: "",
                        created_at: timestamp,
                        email: email,
                        role: "customer",
                        payment_method_id: "",
                        uid: uid,
                        updated_at: timestamp,
                        username: username
                    };

                    usersRef.doc(uid).set(userInitialData).then(async () => {
                        // const sendThankYouMail = functions.httpsCallable('sendThankYouMail');
                        // await sendThankYouMail({
                        //     email: email,
                        //     userId: uid,
                        //     username: username,
                        // });
                        dispatch(push('/'))
                        dispatch(hideLoadingAction())
                    })
                }
            }).catch((error) => {
                dispatch(hideLoadingAction())
                alert('アカウント登録に失敗しました。もう1度お試しください。')
                throw new Error(error)
            })
    }
}
```

firebaseのruleのみをdeployするコマンド  

`firebase deploy --only firestore:rules`

firebaseのmodule not found になる際に対処したこと & 参考にしたサイト

http://shincode.info/2021/08/21/module-not-found-cant-resolve-firebase-app-in/

https://firebase.google.com/docs/web/setup?hl=ja

バージョン違いによるインストール方法の仕方やモジュールがインストールした気になっているが実際されていないかったことが原因  

公式ドキュメントを参照して現在verでのインストール方法を参照し、適切にモジュールがインストールされているか(npm install or yarn add)を行う

react-router モジュールのインストール不備  

`npm i react-router`  

モジュールを完全削除した後に再ダウンロード  

npm i  

module don't build や module not found はインストールエラーであることが多い  
