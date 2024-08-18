# JSON 포맷 입출력
<div style="margin-left: 20px;">
<h2>1. Gson 라이브러리</h2>
<div style="margin-left: 10px;">
<ul>
<li> 정의 구글에서 만든 Json포맷 입출력 라이브러리</li>
<li> 기본구조</li>
<img width="1026" alt="image" src="https://github.com/user-attachments/assets/cca958e9-3059-47b2-98ca-e683d43ccc12">
</ul>
</div>

<h2>2. myApp에 적용하기</h2>
<div style="margin-left: 10px;">
<ul>
<li> gradle 설정하기 </li>
<div>
<ul style="padding-left: 10px">
<li>App에 위치한 gradle.build의 dependencies에 gson 라이브러리를 추가한다.</li>

```gradle
{ implementation 'com.google.code.gson:gson:2.11.0' }
```
</ul>
</div>
<li> Save/ Load 코드 수정하기</li>
<div>
<ul style="padding-left: 10px">
<li>Save 코드수정하기</li>
<ol style="padding-left: 10px" type="1">
<li>Gson을 새로 인스턴스한다.</li>
<li>toJson(List<?>)을 호출한다.</li>
<li>FileWriter로 생성한 객체에 대입한다.</li>
</ol>
<li>load 코드수정하기</li>
<ol style="padding-left: 10px" type="1">
<li>BufferedReader : 한줄 씩 읽어들이는 InputStream 이며, 빈 문자열 일 경우 null을 return한다.</li>
<li>한줄 씩 읽어 line에 대입하고, line이 null이 아닐때까지 반복한다.</li>
<li>line을 StringBuffer에 대입한다. </li>
<li>Gson.fromJson(문자열, 데이터구조)로 구조화 한 후 boadList에 전부 대입한다.</li>
<li>데이터 구조: TypeTokeon(ArrayList(Board))로 ArrayList의 데이터 구조를 말한다. </li>
</ol>

```java
private void loadBoards() {
    try (BufferedReader in = new BufferedReader(new FileReader("board.json"))) {
        StringBuilder strBuilder = new StringBuilder();
        String line;
        while ((line = in.readLine()) != null) {
            strBuilder.append(line);
            }
        boardList.addAll(new Gson().fromJson(strBuilder.toString(), new TypeToken<List<Board>>() {
        }));
        
        int maxBoardNo = 0;
        for (Board board : boardList) {
        if (board.getNo() > maxBoardNo) {
          maxBoardNo = board.getNo();
          }
        }
    
    Board.initSeqNo(maxBoardNo);
    
    } catch (IOException e) {
        System.out.println("게시글 정보 로딩 중 오류 발생!");
        // e.printStackTrace();
        }
    }
    
    private void saveBoards() {
        try (FileWriter out = new FileWriter("board.json")) {
        Gson gson = new Gson();
        out.write(gson.toJson(boardList));
        
    } catch (IOException e) {
        System.out.println("게시글 정보 저장 중 오류 발생!");
        //      e.printStackTrace();
    }
}
```
</ul></div>
<li> 데이터 맞춤 설정하기</li>
<div>
<ul style="padding-left: 10px">
<li>json파일에 데이터 타입이 "createdDate":"Jul 22, 2024, 5:13:25 PM"으로 출력이 된다.</li>
<li> 이를 "yyyy-MM-dd"타입으로 출력하기 위해서 설정이 필요하다.</li>
<li> GsonBuilder을 통해서 설정을 해줄 수 있으며 코드는 다음과 같다</li>

```java
new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create()
```
<li> Save/Load 코드를 수정하면 다음과 같다.</li>

```java
private void loadBoards() {
    try (BufferedReader in = new BufferedReader(new FileReader("board.json"))) {
            StringBuilder strBuilder = new StringBuilder();
            String line;
            while ((line = in.readLine()) != null) {
                strBuilder.append(line);
            }
                boardList.addAll(new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create().fromJson(strBuilder.toString(), new TypeToken<List<Board>>() {
            }));
            
            int maxBoardNo = 0;
            for (Board board : boardList) {
            if (board.getNo() > maxBoardNo) {
                maxBoardNo = board.getNo();
                }
            }
        
        Board.initSeqNo(maxBoardNo);
        
        } catch (IOException e) {
            System.out.println("게시글 정보 로딩 중 오류 발생!");
            // e.printStackTrace();
        }
    }
    
    private void saveBoards() {
        try (FileWriter out = new FileWriter("board.json")) {
          out.write(new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create().toJson(boardList));
        
        } catch (IOException e) {
          System.out.println("게시글 정보 저장 중 오류 발생!");
        //      e.printStackTrace();
    }
}
```
</ul>
</div>
</ul>
</div>
</div>

# Generic 적용하기
<div style="margin-left: 20px;">
<h2>1. Generic의 개념</h2>
<div style="margin-left: 10px;">
<ul>
<li> 결정되지 않은 타입을 파라미터로 처리하고 실제 사용할 때 파라미터를 구체적인 타입으로 대체 시키는 기능을 말한다.</li>
<li> 실행 할 때 사용할 타입을 정하면 캐스팅없이 해당 타입을 다룰 수 있다.</li>

```java
public class Test{
  static class Box<T>{
    private T value;
    public T get(){return this.value;}
    public void set(T value){this.value = value;}
  }
  
  public static void main(String[] args){
    Box<String> box = new Box<>();
    box.set("안녕하세요");
    System.out.println(box.get());
    //안녕하세요
    
    Box<Integer> boxInt = new Box<>();
    box.set(1);
    System.out.println(box.get());
    //1
  }
}
```
</ul>
</div>
<h2>2. Generic의 사용</h2>
<div style="margin-left: 10px;">
<ol>
<li> 배열에 제네릭 적용하기</li>

```java
public class Test {
  static <T> T[] reverse2(T[] arr) {
    for (int i = 0; i < arr.length / 2; i++) {
      T temp = arr[i];
      int targetIndex = arr.length - 1 - i;
      arr[i] = arr[targetIndex];
      arr[targetIndex] = temp;
    }
    return arr;
  }

  public static void main(String[] args) {
    String[] arr2 = reverse2(new String[] {"a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k"});
    for (String value : arr2) {
      System.out.print(value + ",");
    }
    System.out.println();
  }
}

// String[] arr = new String[]{~~~}
```
<li> 제네릭을 이용하여 배열을 직접 생성할 수는 없다. 그렇기 때문에 다른 방법으로 배열을 생성해야한다.</li>
<details>
<summary> 코드 접기/펴기</summary>

```java
// 제네릭(Generic) : 배열 만들기
import java.lang.reflect.Array;
import java.util.Arrays;

public class Test {
  // 예1) 제네릭의 타입 파라미터로 레퍼런스 배열을 생성할 수 없다.
  static <T> T[] create1() {
    T[] arr;
    //    arr = new T[10]; // 컴파일 오류! new 명령어를 사용할 때 제네릭의 타입 파라미터를 사용할 수 없다.
    return null;
  }

  // 예2) 견본 배열을 받아서 복제하는 방법을 사용한다.
  static <T> T[] create2(T[] arr) {
    return Arrays.copyOf(arr, 10);
  }

  // 예3) 배열의 타입 정보를 받아 생성하기
  static <T> T[] create3(Class<?> type) {
    return (T[]) Array.newInstance(type, 10);
  }

  // 예4) 견본 배열에서 타입 정보를 추출하여 배열을 생성하기
  static <T> T[] create4(T[] arr) {
    Class<?> arrayTypeInfo = arr.getClass(); // 예) String[]
    System.out.println(arrayTypeInfo);

    Class<?> arrayItemTypeInfo = arrayTypeInfo.getComponentType(); // 예) String
    System.out.println(arrayItemTypeInfo);

    return (T[]) Array.newInstance(arrayItemTypeInfo, 10);
  }

  public static void main(String[] args) {
    // 제네릭을 사용하는 메서드를 이용하여 배열 만들기

    // 파라미터로 빈 배열을 넘기면,
    String[] arr1 = create2(new String[0]);
    System.out.println(arr1.length);

    // 내부에서 생성할 배열 크기 보다 더 큰 배열을 파라미터로 넘긴다면?
    // copyOf() 그래도 새 크기에 맞춰 새 배열을 생성한다.
    String[] temp = new String[100];
    String[] arr2 = create2(temp);
    System.out.println(arr2.length);
    System.out.println(temp == arr2);

    // 생성할 배열의 타입 정보를 넘긴다.
    String[] arr3 = create3(String.class);
    System.out.println(arr3.length);

    // 배열을 넘기면 배열의 항목 타입을 알아내어 새 배열을 만든다.
    String[] arr4 = create4(new String[0]);
    System.out.println(arr4.length);
  }
}
```
</details>
</ol>
</div>
<h2>3. Generic의 특징</h2>
<div style="margin-left: 10px;">
<ul>
<li> Generic은 다루는 타입을 제한 할수 있다.</li>
<li> Generic은 캐스팅에서 자유롭다.</li>

```java
List<String> arr = new ArrayList<>();
arr.add("String");
//arr.add(new Integer(100)) -> 불가능
String s = arr.get(0); // (String으로 캐스팅을 안해도됨)
```
</ul>
</div>
<h2>4. Generic : 파라미터 타입 인스턴스</h2>
<div style="margin-left: 10px;">
<ul>
<li> 컴파일 과정에서 T가 결정되면 바꿀수 없다.</li>
<li> 예를 들어 T가 String 이면 오직 String만 올 수 있다.</li>

```java
public class Test {

  static class Box<T> {
    void set(T obj) {}
  }

  public static void main(String[] args) {
    Box<String> box1 = new Box<>();
    Box<Integer> box2 = new Box<>();
    Box<Member> box3 = new Box<>();

    //    box1 = new Box<Object>(); // 컴파일 오류!
    //    box2 = new Box<String>(); // 컴파일 오류!
    //    box3 = new Box<Object>(); // 컴파일 오류!
  }
}
```
<li> 다음과 같이 상속 관계가 있을때, T의 상속옵션에 따라 생성 가능 여부가 바뀐다.</li>

![image](https://github.com/user-attachments/assets/e96c0232-7390-49fd-936a-52b6b261f9e9)
```java
public class Test {

  static class A {}
  static class B1 extends A {}
  static class B2 extends A {}
  static class C extends B1 {}

  static class Box<T> {
    void set(T obj) {}
  }
}
```
<li> T = B1 일때 : B1만 가능</li>

```java
  public static void main(String[] args) {
    Box<B1> box1;

    //    box1 = new Box<Object>(); // 컴파일 오류!
    //    box1 = new Box<A>(); // 컴파일 오류!
    box1 = new Box<B1>();
    //    box1 = new Box<C>(); // 컴파일 오류!
  }
```
<li> T = ? extends B1 일때 : B1 아래는 다 가능</li>

```java
  public static void main(String[] args) {
  Box<? extends B1> box1;

  //    box1 = new Box<Object>(); // 컴파일 오류!
  //    box1 = new Box<A>(); // 컴파일 오류!
  box1 = new Box<B1>();
  box1 = new Box<C>();
```
<li> T = ? super B1 일때 : B1 위는 다 가능</li>

```java
  public static void main(String[] args) {
  Box<? super B1> box1;

  box1 = new Box<Object>();
  box1 = new Box<A>();
  box1 = new Box<B1>();
  //    box1 = new Box<C>(); // 컴파일 오류!

}
```
</ul>
</div>
<h2>4. Generic : 파라미터 타입 레퍼런스</h2>
<div style="margin-left: 10px;">
<ul>
<li>레퍼런스를 선언 하지 않으면 원시타입 그대로 사용하여, 모든 타입에 대한 객체를 생성할 수 있고, 메서드 파라미터에 모든 타입을 사용할 수 있다.</li>

```java
    // 레퍼런스를 선언할 때 제네릭 타입을 지정하지 않으면 
    // 객체 생성시 어떤 제네릭 타입을 지정하더라도 상관없다.
    ArrayList list1; 
    //    list1 = new ArrayList(); // OK
    //    list1 = new ArrayList<>(); // OK
    //    list1 = new ArrayList<Object>(); // OK
    //    list1 = new ArrayList<String>(); // OK
    list1 = new ArrayList<Member>(); // OK

    //=> 레퍼런스를 선언할 때 제네릭 타입을 지정하지 않으면 
    //   ArrayList 객체를 생성할 때 지정한 제네릭 타입은 무시된다.
    list1.add(new String());
    list1.add(new Integer(100));
    list1.add(new java.util.Date());
    list1.add(new Member("홍길동", 20));
```
<li>레퍼런스를 와이드카드(?)로 선언되면 모든 타입에 대한 객체를 생성할 수 있으나, 파라미터 타입으로는 사용 할 수 없다.</li>
<li>컴파일 시에 ?에 대한 타입을 확정 할 수 없기 때문이다.</li>

```java
   // 레퍼런스를 선언할 때 제네릭 타입을 <?> 로 선언하면
    // 객체 생성시 어떤 제네릭 타입을 지정하더라도 상관없다.
    // 단, 메서드를 사용할 때 주의해야 한다.
    ArrayList<?> list2; 
    //    list2 = new ArrayList(); // OK
    //    list2 = new ArrayList<>(); // OK
    //    list2 = new ArrayList<Object>(); // OK
    //    list2 = new ArrayList<String>(); // OK
    list2 = new ArrayList<Member>(); // OK

    //=> 레퍼런스 선언할 때 제네릭 타입을 ? 로 설정했기 때문에
    //   add() 메서드의 파라미터 타입은 ? 가 된다.
    //   즉 파라미터 타입이 뭔지 정확하게 설정되지 않았기 때문에
    //   컴파일러는 문법의 유효여부를 검사할 수 없다.
    // 
    list2.add(new String()); // 컴파일 오류!
    list2.add(new Integer(100)); // 컴파일 오류!
    list2.add(new java.util.Date()); // 컴파일 오류!
    list2.add(new Member("홍길동", 20)); // 컴파일 오류!
```
<li>레퍼런스를 Object로 선언되면 Object로 선언한 타입만 객체를 생성할 수 있으나, 파라미터 타입으로는 모든 타입을 사용 할 수 있다.</li>

```java
    ArrayList<Object> list1;
    // 제네릭 타입을 Object로 지정하면 
    // 그렇게 지정된 ArrayList 객체만 list1에 저장할 수 있다.

    //    list1 = new ArrayList(); // 이렇게 사용하지 말고, 명확히 제네릭의 타입을 지정하라.
    list1 = new ArrayList<Object>();
    list1 = new ArrayList<>();
    //    list1 = new ArrayList<String>(); // 컴파일 오류!
    //    list1 = new ArrayList<Integer>(); // 컴파일 오류!


    // 메서드를 호출할 때는 레퍼런스에 지정된 제네릭 타입으로 문법을 검사한다.
    list1.add(new String()); 
    list1.add(new java.util.Date());
    list1.add(new Integer(100));
    list1.add(new StringBuffer());
```
</ul>
</div>

<h2>5. myApp에 적용하기</h2>
<div style="margin-left: 10px;">
<ul>
<li> 유저리스트, 보드리스트, 프로젝트리스트는 모두 동일한 코드를 사용한다.</li>
<li> 메서드에 넘기는 매개변수만 차이를 보인다.</li>

```java
  private <E> void loadJson(List<E> list, String filename, Class<E> elementType) {
    try (BufferedReader in = new BufferedReader(new FileReader(filename))) {

      StringBuilder strBuilder = new StringBuilder();
      String line;
      while ((line = in.readLine()) != null) {
        strBuilder.append(line);
      }

      list.addAll((List<E>) new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create()
          .fromJson(strBuilder.toString(),
              TypeToken.getParameterized(List.class, elementType).getType()));

      for (Class<?> type : elementType.getInterfaces()) {
        if (type.equals(InitSeqNo.class)) {
          intintSeqNo(list, elementType);
        }
      }

    } catch (Exception e) {
      System.out.printf("%s 정보 로딩 중 오류 발생!\n", filename);
      e.printStackTrace();
    }
  }

  private <E> void intintSeqNo(List<E> list, Class<E> elemnetType) throws Exception {
    int maxSeqNo = 0;
    for (Object element : list) {
      InitSeqNo seqNo = (InitSeqNo) element;
      maxSeqNo = Math.max(seqNo.getNo(), maxSeqNo);
    }
    Method method = elemnetType.getMethod("initSeqNo", int.class);
    method.invoke(null, maxSeqNo);
  }

  private <E> void saveJson(List<E> list, String filename) {
    try (FileWriter out = new FileWriter(filename)) {
      out.write(new GsonBuilder().setDateFormat("yyyy-mm-dd HH:mm:ss").create().toJson(list));
    } catch (IOException e) {
      System.out.printf("%s 저장 중 오류발생!", filename);
    }
  }
```
<li> saveJson과 loadJson 메서드를 호출은 다음과 같다.</li>

```java
  private void loadData() {
    loadJson(userList, "user.json", User.class);
    loadJson(projectList, "porject.json", Project.class);
    loadJson(boardList, "board.json", Board.class);
    System.out.println("데이터를 로딩 했습니다.");
  }


  private void saveData() {
    saveJson(userList, "user.json");
    saveJson(projectList, "porject.json");
    saveJson(boardList, "board.json");
    System.out.println("데이터를 저장 했습니다.");
  }
```
</ul>
</div>
</div>
