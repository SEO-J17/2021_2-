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