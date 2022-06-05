# 채팅 앱 (잇지)
+ React-Native 프레임 워크를 이용하여 구현한 채팅 앱
+ 관리자가 비콘을 두고, 반경을 설정해서 비콘 반경내에 있는 사용자들끼리 채팅을 함
+ 공개채팅방에서 리스트를 통해서 참여 하고 있는 사용자들을 나타내고 친구 요청을 할 수 있다.
+ 친구 요청을 수락하여 친구가 되면, 1:1 개인 채팅을 할 수도 있다.
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
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/148978105-659299e3-9f17-477b-842a-bfea15fc50a7.png></td>
<td><img src=https://user-images.githubusercontent.com/59912150/148978108-44af960a-d9d5-41b6-8441-db910562ae79.png></td>
</tr>
</table>

+ 공개 채팅방에서 친구 초대를 보내고 상대방이 수락하면 친구 목록에 나타나며 1대1 개인 채팅을 할 수 있음
+ 기존에는 1대1 개인 채팅을 하게되면 해당 채팅방에 초대가 불가능 했음
+ 1대1 채팅방에서 오른쪽 상단 버튼을 누르게 되면 친구 초대하기 버튼이 나타나게 기능을 추가함
+ 초대하기 버튼을 누르게 되면 이미 채팅방에 들어와 있는 친구들은 반투명으로 클릭이 불가능하게 함
+ 친구 초대 기능은 Firebase를 이용하여 구현함
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
          .child(친구 요청 수락 child)
          .val();
        if (친구 요청 수락 === 거절) {
          setUserBlocked(true);
        }
      }
    });

  return userExist || userBlocked;
}
```

+ 위 코드는 채팅방에 초대가 가능한지 확인하는 함수이다.
+ Firebase의 유저 테이블에 있는 친구 목록 정보를 확인하여 차단되어 있다면, 초대가 불가능 하게 되어있는것을 볼 수 있다.

#

<br>
</br>

>## 2. 채팅방 인원 제한 기능 구현
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/172060394-782e9563-ed84-4249-bd9f-872140654046.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/172060397-5f571b86-ded7-4411-a63b-42173a734ce5.jpg></td>
</tr>
</table>

+ 채팅방 이름 옆에 현재 채팅방에 입장한 인원 수와 입장 가능한 최대 인원 수를 숫자로 표시 하였다.
+ 현재 채팅방에 있는 인원 수가 최대 인원 수 일때, 만약 입장하려고 한다면 알림 창을 띄워서 입장을 불가하게 하였다.

<br>

> 공개 채팅방 인원 수 파이어베이스 코드

``` js
function ChattingPeople(채팅방 아이디: any) {
  const [people, setPeople] = useState(0);
  const [maximum, setMaximum] = useState(0);

  useEffect(() => {

    //관리자가 설정한 채팅방 허용인원 수 가져옴
    FirebaseRefs.채팅방 참조
      .child('공개채팅방')
      .child(채팅방 아이디)
      .child('참여 가능한 최대 인원 수')
      .on('value', (snapshot) => {
        if (snapshot.exists()) {
          setMaximum(snapshot.val());
        }
      });


    //처음 채팅방에 참여중인 사람.
    FirebaseRefs.채팅방 참조
      .child('공개채팅방')
      .child(채팅방 아이디)
      .child('users')
      .on('child_added', function (data) {
        if (data.exists() && data.key !== null) {
          FirebaseRefs.채팅방 참조
            .child('공개채팅방')
            .child(채팅방 아이디)
            .child('users')
            .once('value', (snapshot) => {
              if (snapshot.exists()) {
                if (data.key !== null) {
                  if (
                    snapshot.child(data.key).child('status').val() === 'ONLINE'
                  ) {
                    setPeople((people) => people + 1);
                  }
                }
              }
            });


          //유저 온라인,오프라인 상태 변경시 실행.
          FirebaseRefs.채팅방 참조
            .child('공개채팅방')
            .child(채팅방 아이디)
            .child('users')
            .child(data.key)
            .on('child_changed', (snapshot) => {
              if (snapshot.val() === 'ONLINE') {
                setPeople((people) => people + 1);
              } else if (snapshot.val() === 'OFFLINE') {
                setPeople((people) => people - 1);
              }
            });
        }
      });
  }, []);

  return [people, maximum];
}
```

+ 위 코드에 나타나 있는 ChattingPeople 함수의 코드는 공개 채팅방 리스트를 받아올때 실행되는 함수 안에 있는 함수 이다.
+ 파이어베이스를 통해서 관리자가 설정한 참여 가능 한 최대 인원수 데이터를 받아와 maximum이라는 변수에 저장한다.
+ 제일 처음으로 입장하고자 하는 채팅방에 참여중인 사용자 데이터를 받아오기위해서, 해당 공개 채팅방에 들어가 있는 사용자들의 상태를 체크하여 온라인 상태인 사람을 people 이라는 변수를 증가시키면서 people 변수에 채팅에 참여 중인 사용자 수를 저장한다.
+ 그 후 사용자가 온라인, 오프라인 상태에 변화가 있다면 다시 계산을 하여 online인 사람은 people이라는 변수에 +1을 하여 저장하고 만약 offline 이라면 people에서 -1 을 하여 현재 인원수 데이터를 people변수에 저장한다.
+ 현재 입장한 사용자 수와, 입장 가능 한 최대 인원 수를 파이어베이스에서 받아오면 각각의 데이터를 리턴한다.

<br>
<br>

```js
function 공개채팅방 리스트 함수({
  onPress,
  item,
}: IPublicRoomListItemProps): ReactElement {
  const [people, capacity] = ChattingPeople(채팅방 아이디);

  return (
    <TouchableOpacity
      activeOpacity={0.5}
      onPress={() =>
        capacity && people >= capacity && capacity > 0
          ? Alert.alert('알림', '방 허용 인원이 다 찼습니다.')
          : onPress()
      }>
      <View style={styles.roomItem.container}>
        {/*콘텐츠(이름, 유저 숫자)*/}
        <View style={styles.roomItem.content}>
          <Text style={styles.roomItem.name}>
            {item.name}
            {'  '}
          </Text>
          {capacity > 0 && [
            <Text style={styles.roomItem.count}>{people}</Text>,
            <Text style={styles.roomItem.count}>{'/' + capacity}</Text>,
          ]}
        </View>
        {/*방 이미지*/}
        <Image style={styles.roomItem.icon} source={Images.icon} />
      </View>
    </TouchableOpacity>
  );
}

```
+ 위 코드는 공개채팅방 리스트를 나타내는 함수의 코드를 나타낸것이다.
+ 앞서 설명 파이어베이스 코드를 통해서 현재 해당 채팅방에 입장한 사용자의 수를 people, 참여 가능한 최대 인원 수를 capacity 변수에 저장한다.
+ 그 후에 view를 통해서 현재 채팅방에 입장한 사용자의 수와 최대 인원수를 나타내었다.
+ 현재 입장한 사용자 수는 최대 인원 수를 넘을 수 없기 때문에, 채팅방을 클릭하는 onPress 이벤트에서 삼항 조건 연산자를 통해 만약 현재 입장한 사용자 수가 최대 인원수 보다 같거나 크게 된다면 Alert를 통해서 허용 인원을 초과한다는 알람 창을 띄우게 되면서 입장을 거부시킨다.

#

<br>

>## 3. 채팅방 비밀번호 기능 구현
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/172062009-58893d8f-9550-4fc8-b6cd-cf875ac2d557.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/172062010-fecc159b-a914-4e66-b114-20541dbbbd8e.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/172062012-7c60771c-c18a-43c0-b175-dc539543585b.jpg></td>
</tr>
</table>