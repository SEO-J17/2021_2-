# 채팅 앱 (잇지)
+ React-Native 프레임 워크를 이용하여 구현한 채팅 앱
+ 관리자가 비콘을 두고, 반경을 설정해서 비콘 반경내에 있는 사용자들끼리 채팅을 함
+ Firebase를 이용하였고 관리자 페이지를 따로 만들어 반경 설정과 채팅방 관리를 함
+ <mark><u>**_소스코드는 회사 보안상의 이유로 정확한 코드로 명시하지 않았음_**</u></mark>
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/148651309-366ba8dc-8a10-425e-b534-6628363855a2.png></td>
<td><img src=https://user-images.githubusercontent.com/59912150/148651249-03ec9b16-5c7b-4388-8dfd-37405b304a49.png></td>
</tr>
</table>

<br>
<br>

# 개발 내용
>## 1. 채팅방 초대 기능 구현
+ 공개 채팅방에서 친구 초대를 보내고 상대방이 수락하면 친구 목록에 나타나며 1대1 개인 채팅을 할 수 있음
+ 기존에는 1대1 개인 채팅을 하게되면 해당 채팅방에 초대가 불가능 했음
+ 1대1 채팅방에서 오른쪽 상단 버튼을 누르게 되면 친구 초대하기 버튼이 나타나게 기능을 추가함
+ 초대하기 버튼을 누르게 되면 이미 채팅방에 들어와 있는 친구들은 반투명으로 클릭이 불가능하게 함
+ 친구 초대 기능은 Firebase를 이용하여 구현함

<br>

<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/148978105-659299e3-9f17-477b-842a-bfea15fc50a7.png></td>
<td><img src=https://user-images.githubusercontent.com/59912150/148978108-44af960a-d9d5-41b6-8441-db910562ae79.png></td>
</tr>
</table>

+ 오른쪽상단 버튼을 누르게 되면 다음 사진과 같이 친구 목록이 나타나고, Touchable Opacity로 친구 초대 버튼을 구현함.
+ 채팅방에 있는 사람들을 리스트로 나타낼때는 FlatList를 이용했다.
+ 친구 초대 버튼은 1대1 채팅방에만 활성화 되게 조건을 추가했음

<br>

>사이드 메뉴바 코드
``` js
roomtype === '개인채팅방' && (
          <TouchableOpacity onPress={초대 기능 함수 실행 부분}>
            <View style={styles.item}>
              <Image style={styles.inviteImage} source={InviteIcon}/>
              <Text style={styles.inviteText}>새로운 친구 초대</Text>
            </View>
          </TouchableOpacity>

<FlatList
    data={유저리스트}
    bounces={false}
    renderItem={({item}) => (
     <유저리스트 컴포넌트 item={item} onPress={props.onItemPress} />
      )}
    keyExtractor={(item) => item.유저아이디}/>    

function 유저리스트 컴포넌트({item, onPress}: 아이템인터페이스) {
  const user = {
    _id: item.userId,
    name: item.username,
    avatar: PhotoUtil.getUserThumbURL(item),
    photoURL: PhotoUtil.getUserPhotoURL(item),
    facebook: item.facebook,
    intsagram: item.instagram,
  };

  return (
    <TouchableOpacity onPress={() => onPress(user)}>
      <View style={styles.item}>
        <GiftedAvatar onPress={undefined} user={user} />
        <Text style={styles.itemText}>{user.name}</Text>
      </View>
    </TouchableOpacity>
  );
}
```
<br>

#

<br>

<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/148980346-fa9d767f-e833-4dd9-9cb2-57b291fada27.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/148980322-2d7d8c5d-b1e9-4db0-83bd-67b3d9c60ba9.jpg></td>
</tr>
</table>

+ 위 그림은 친구 초대 버튼을 눌렀을 때 나타나는 친구목록 화면이다
+ 친구 목록은 Firebase에서 불러온 유저 정보 각각을 TouchableOpacity로 구성했다. 그리고 RadioButton 라이브러리를 이용하여 버튼을 추가 하여 구현했다.
+ Firebase 코드를 이용해서 채팅방에 이미 있는 사용자는 조건 연산자를 이용해서 초대가 안되게 반투명으로 표시하여 클릭을 막아놨다.
+ list를 하나 선언하여 클릭이 된 유저의 아이디 값을 list에 담고, 만약 같은 유저가 한 번더 클릭이 됐다면 list에 있는 ID값을 list 에서 제거한다. 
+ list에 유저 ID값을 저장하고 삭제하는 방식으로 구현하여 상단의 확인 버튼을 최종적으로 누르게 되면 Firebase로 유저ID값과 해당 방의 ID값을 보내서 유저들을 초대한다.
<br>
</br>
> 친구 초대 리스트 코드 
``` js
function InviteCheck(유저아이디값: any) {
    //이미 들어간 값이 있으면 리스트에서 해당 id 삭제
    if (리스트.includes(유저아이디값)) {
      리스트.pop(유저아이디값);
    } else {
      리스트.push(유저아이디값);
    }
  }

function ChatUserListItem({item, roomId, onPress}: IChatUserListItem) {
  const user = {
    userId: 유저아이디 값
    username: 유저 이름
  };

  const [checked, setChecked] = useState(false);
  const isTouchable = isInvitable(user.userId, roomId);

  return (
    <TouchableOpacity
      disabled={isTouchable}
      onPress={() => onPress(유저아이디 값, setChecked(!checked))}>
      <View
        style={isTouchable ? styles.screen.disableditem : styles.screen.item}>
        <RadioButton
          disabled={isTouchable}
          value="first"
          status={checked ? 'checked' : 'unchecked'}
          onPress={() => onPress(유저아이디 값, setChecked(!checked))}
        />
        <GiftedAvatar user={user} />
        <Text style={styles.screen.itemText}>{유저이름}</Text>
      </View>
    </TouchableOpacity>
  );
}
```
+ 제일 첫 번째 줄InviteCheck 함수는 앞서 설명했던 것 처럼 리스트를 하나 선언하고 유저가 클릭될 때 마다 리스트에서 삭제하고 추가하여 나중에 최종적으로 초대를 할때 리스트를 Firebase에 전달 되도록 했다.
+ 위 코드처럼 리스트는 TouchableOpacity로 구현했고 isInvitable이라는 함수를 통해 이미 방에 초대하고자 하는 유저가 존재 한다면 boolean값으로 받아 false면 클릭이 안되게 구현했다
+ 방 유무 뿐만 아니라 자신을 차단한 상대도 초대가 불가능 하게 구현했다


<br>
</br>

> 친구 초대 가능 확인 파이어베이스 코드

``` js
function isInvitable(유저아이디값: string, 방 아이디: string) {
  const [userExist, setUserExist] = useState(false);
  const [userBlocked, setUserBlocked] = useState(false);

  Firebase.방 정보
    .child('채팅방')
    .child(방아이디)
    .once('value', (snapshot) => {
      if (snapshot.exists()) {
        if (snapshot.child(유저).child(유저아이디).exists()) {
          setUserExist(true);
        }
      }
    });

  //나를 차단을 한 상대 초대X
  Firebase.usersRef
    .child('친구목록')
    .once('value', (snapshot) => {
      if (snapshot.exists()) {
        const acceptBy = snapshot
          .child(fbAuth.currentUser!.uid)
          .child('accepted')
          .val();
        if (acceptBy === 'blocked') {
          setUserBlocked(true);
        }
      }
    });

  return userExist || userBlocked;
}
```

