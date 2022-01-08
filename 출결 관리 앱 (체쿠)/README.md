# 출결 관리 앱 (체쿠 학생용)
+ 물리적으로 비콘을 설치하여 모바일의 위치 정보를 통한 자동 출결 앱
+ 과제 부여 및 알림 기능을 통해 편리하게 학생들에게 할당 가능
+ 학생 본인의 출결 관리기능 지원
+ React-Native 프레임워크를 이용하여 개발 진행
+ 웹 서버는 자사 서버를 이용
+ <mark>**_소스코드는 회사 보안상의 이유로 정확한 코드로 명시하지 않았음_**</mark>

<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/146143248-c28291af-0574-42d0-bafe-9a6f3475b5bf.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/146143261-db85626a-535c-4ecd-a174-c178c1b77086.jpg></td>
<td><img src=https://user-images.githubusercontent.com/59912150/146143271-54490e30-b495-4926-931f-17be35dec80c.jpg></td>
</tr>
</table>

<br>
</br>

#   개발 내용
> ## 1. 검색 기능 오동작 이슈 처리
 + 메인 로그인 화면에서 학교 검색을 하면 검색이 되질 않는 이슈 발생
 + 검색 기능을 실행하는 함수에서 콘솔을 찍어보며 디버깅 진행
 + 콘솔을 찍어보니 검색을 마칠때 공백이 들어가는 것을 발견
 + 모바일 키보드에서 Enter를 눌러야 검색이 되기 때문에 Enter가 공백으로 인식된 것 이라고 생각
<br>
</br>

 > ## 해결 방법

 ``` js
 const goSearch = (value: string) => {
    if (value.length !== 0) {
      page(-1)
      keyword(value)
    } else {
      page(-1)
    }
};
 ```
 
+ 검색을 실행하는 함수에 if-else문을 적용
+ 검색을 위해 입력한 text를 value라는 파라미터로 받아 이 값이 길이가 0이 아닐때만 검색 결과 페이지를 set하고, 검색 결과를 반영하게 코드를 수정함
<br>
</br>

 > ## 결과 화면
+ 입력한 키워드의 값이 성공적으로 검색이 되는 것을 확인가능
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/146146401-3e6c6151-03d1-4483-adac-cbbcdf7e68ee.jpg>
</td>
<td><img src=https://user-images.githubusercontent.com/59912150/146146398-882d6da5-5191-4b38-8e0c-ca631af0ccae.jpg>
</td>
</tr>
</table>
<br>
</br>

>## 2. UI개발 
+ 알림,과제 등이 있을 경우 해당 과목들의 알림이 리스트 형식으로 나타나도록 UI를 개발
+ 리스트 형식으로 구현하기 위해 FlatList를 이용
+ 라이브러리를 이용해 ListItem을 사용하여 각 리스트의 아이템을 설정
+ [사용한 라이브러리 링크](https://reactnativeelements.com)

 ``` js
 <FlatList
    refreshing={api}
    onRefresh={() => setOffset(-1)}
    onEndReached={() => setOffset(offset + 1)}
    keyExtractor={(item, index) => index.toString()}
    data={api.items}
    ListEmptyComponent={() => (<Text style={styles.listEmptyText}>알림이 없습니다.</Text>)}
    renderItem={({ item, index }) => (
    {() => (
      <ListItem bottomDivider onPress={() => {
         item.markRead(() => { });
         props.navigation.navigate('SomethingScreen', {
         item: item
         });
    }}>
      <ListItem.Content>
            <ListItem.Title numberOfLines={1} style={styles.title}>{item.title}</ListItem.Title>
            <ListItem.Subtitle>{moment(item.createDate, 'YYYY-MM-DD HH:mm').format('YYYY년 M월 D일 (ddd) HH:mm')}</ListItem.Subtitle>
       </ListItem.Content>
    </ListItem>
     )}
    )}
  />
 ```
 <br>
 </br>

 >## UI  개발 결과 화면
+ 처음 알림 화면과 알림을 눌렀을때 나타나는 상세 화면
<table>
<tr>
<td><img src=https://user-images.githubusercontent.com/59912150/147946959-8f2c2d98-e63c-4c04-8f5e-b8089fbb16e5.jpg>
</td>
<td><img src=https://user-images.githubusercontent.com/59912150/147943945-fb87836c-6936-4689-82ec-058873089bd3.jpg>
</td>
</tr>
</table>
<br>
</br>

>## 3. 알림 상세 화면 개발 
+ 알림이 주어졌을 때 해당 알림을 클릭하면 알림내용과 타입을 볼 수 있게 함
+ 만약, 알림 타입이 과제라면, 과제 제출 남은 기한과 제출 할 수 있는 버튼을 만들어 과제를 제출을 할 수 있게 구현
+ <mark>**_실습 기간이 끝난 관계로 프로젝트에 제외되어 결과 화면을 볼 수 없어 소스코드로 대체_**</mark>

<br>

> ### 알림 상세 화면 코드
``` js
<Fragment>
  <SafeAreaView style={{ flex: 0, backgroundColor: Constants.statusbarColor }} />
    <SafeAreaView style={styles.container}>
      <StatusBar
        animated={true}
        backgroundColor={Constants.statusbarColor}
        barStyle="dark-content"
        />
      <Header
        title={`${subjectName} 알림방`}
        onBackPress={() => {
        props.navigation.goBack();
        }}></Header>
        <View style={styles.titleContainer}>
            <View style={styles.deadLineView}>
            <Text style={styles.createDate}>
              {moment(notice.createDate, 'YYYY-MM-DD HH:mm').format('YYYY년 M월 D일 (ddd) HH:mm')}
            </Text>
            <Text>{notice.assignmentDone ? '제출완료' : notice.deadlineText}</Text>
          </View>
        </View>

        {notice.type === '과제' && (
          <Button
            title="과제 제출"
            buttonStyle={styles.button}
            onPress={APILINK}
          />
        )}
      </SafeAreaView>
    </Fragment>
 ```
 + notice.type === '과제' && 코드를 이용하여 만약 해당 알림이 과제라면 과제 제출을 가능 하게 하는 버튼을 만듦

 <br>

 
> ### 과제 제출 API 부분 코드
 ``` js
 const APILINK = async () => {
  const amsStudentLink = (await API.getServer()).replace('checkoo.co.kr', '체쿠 학생 홈페이지');
  Alert.alert(
    '확인',
    '과제제출을 위해 체쿠 학생지원시스템으로 이동합니다.\nPC에서도 학생지원시스템을 이용해 과제제출이 가능합니다.',
    [
      {
        text: '취소',
      },
      {
        text: '링크복사',
        onPress: async () => {
          Clipboard.setString(student_Link);

          Alert.alert('링크복사', '링크가 클립보드에 복사되었습니다.', [
            {
              text: '확인',
            },
          ]);
        },
      },
      {
        text: '이동',
        onPress: () => {
          Linking.openURL(student_Link);
        },
      },
    ],
  );
}
 ```
 + 버튼을 누르면 만든 API를 통해서 자사 서버로 전송되게 하였음
 + 과제 제출은 연결된 홈페이지로 이동해서 과제제출을 하게 구현

<br>
<br>

# 느낀 점
+ '체쿠'는 채팅앱인 '잇지'를 먼저 수행하고 진행한 두 번째 앱 이었다. <br>
React-Native을 현장실습을 시작하면서 처음 다뤄봤기 때문에 '잇지'를 
개발할 때 하고는 다르게 나름 수월하다고 느낀 프로젝트 였다.

+ '잇지' 가 메인 프로젝트 였기 때문에 비교적 구현 하고자하는 기능들이 쉬워서 때문일 수도 있지만 처음 시작할 때를 생각하면 많이 발전했다고 느꼈다. 

<br>
<br>

> ## 아쉬운 점
<br>

 사실 학생용 체쿠 앱, 교사용 체쿠 앱 총 2개가 있었다. 두 개의 앱 모두 개발에 참여 했다. 
 <br>
학생용 앱은 3일 이라는 짧은 시간안에 주어진 이슈와 기능들을 모두 구현했다.

<br>
체쿠 개발 기간은 약 한달 동안 개발을 했는데, 여기서 3일을 제외한 나머지 기간을 교사용 앱 개발에 시간을 투자했다.
<br>
<br>

교사용 앱에는 출석부 가능 구현을 맡았는데 매우 어려웠던 것으로 기억한다.
처음에는 [테이블 라이브러리](https://github.com/Gil2015/react-native-table-component)를 이용하여 출석부를 구현했는데, 학생수와 나타내는 날짜가 많다보니 랜더링이 너무 느렸다. 
<br>
<br>
그래서 라이브러리를 사용하지 않고 View를 하나씩 랜더링 하는 방식으로 FlatList를 이용하여 구현했지만 출석 값이 들어가게 되면 여전히 느렸다.
<br>
<br>
FlatList, map, 라이브러리, 따로 컴포넌트 분리 등 여러 방식으로 해결을 하려고 했지만 결국 해결을 못했다.
<br>
<br>
실습이 끝나기 일주일 전, 결국 대표님이 모든 날짜와 학생정보, 출석 값을 한 번에 한 화면에 나타내는 것은 무리라고 판단하였고, 한 날짜에 해당하는 출석정보만 나타내기로 요구사항을 바꿔서 구현하기로 했다.
<br>
<br>
하지만, 그것을 해결하기 위해서는 나에게는 시간도 더 필요했고, React-Native에 대한 더 깊은 지식의 상태 관리 개념을 요구 했기 때문에 내 수준에서는 해결 할 수 없는 이슈였다. 
<br>
<br>
결국에는 대표님이 구현을 하셨고 나는 해당 코드를 분석하는 것에서 교사용 체쿠앱 개발을 끝으로 실습을 마쳤다.
<br>
<br>
'잇지'는 구현할 여러 기능과 수정 할 부분이 많아서 2달 반동안 시간을 투자했지만, 체쿠 교사용 앱은 출석부 구현 하나만 하면 되는 것이 었는데, 한 달이라는 시간을 쏟고도 랜더링 문제를 해결을 하지 못한것이 가장 아쉬운점으로 남고, 아직 공부가 많이 필요하다고 생각했다.


















