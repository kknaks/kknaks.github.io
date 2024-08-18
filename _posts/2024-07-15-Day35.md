<h1> Composit Pattern </h1>
<h2 style="margin-left: 10px;"> 1.컴포짓 패턴의 개념 </h2>
<h3 style="margin-left: 20px;"> 1) 정의 </h3>
<ul>
<li> 객체들의 트리를 구성하여 부분-전체 계층 구조를 나타내는 패턴이다.</li>
<li> <b>Component</b> : 공통 인터페이스를 정의하여 단일 객체와 복합 객체가 동일한 방식으로 처리될 수 있도록 한다. </li>
<li> <b>Leaf</b> : 트리의 말단 요소로 더이상 하위 요소를 가지지 않는 객체를 나타낸다.</li>
<li> <b>Composite</b> : 하위 요소를 가지는 복합 객체로, 하위 요소들을 관리하고 해당 요소들에게 작업을 전달한다.</li>
</ul>
<h3 style="margin-left: 20px;"> 2) UML으로 이해하기 </h3>
<ul>
<li> 패턴 적용 전 : 여러객체에서 중복된 코드가 발생하며, 하나의 객체가 여러개의 역할을 한다.</li>

![image](https://github.com/user-attachments/assets/a1f77d26-abd6-484f-8496-240b1b081da5)

<li> 패턴 적용 후 : 기능별/역할별로 분리</li>

![image](https://github.com/user-attachments/assets/a2a18ae5-f1ce-4d6a-8fb5-ca42e2d33a0d)
</ul>

<h2 style="margin-left: 10px;"> 2.MyApp에 적용하기 </h2>
<h3 style="margin-left: 20px;"> 1) Menu생성하기 </h3>
<ol>
<li> 메뉴 인터페이스만들기</li>
<ul style="padding-left: 10px">
<li> 추상메서드 execute()가 필요하다.</li>
<li> 메뉴들의 title을 불러올 규칙이 필요하다.</li>

```java
public interface Menu {
  void execute();

  String getTitle();
}
```

</ul>
<li> 메뉴 추상클래스 만들기</li>
<ul style="padding-left: 10px">
<li> 메뉴그룹과 메뉴아이템의 제목이 들어 올때 저장 할 필드 값 title 필요하다.</li>
<li> title을 불러올 getter가 필요하다.</li>
<li> 트리노드에서 부모노드의 객체와 같은 확인하는 메서드 equals()와 hash()가 필요하다.</li>
<details>
<summary> 코드 접기/펼치기</summary>

```java
import java.util.Objects;

public abstract class AbstractMenu implements Menu {
  protected String title;

  public AbstractMenu(String title) {
    this.title = title;
  }

  @Override
  public boolean equals(Object object) {
    if (this == object)
      return true;
    if (!(object instanceof AbstractMenu that))
      return false;
    return Objects.equals(title, that.title);
  }

  @Override
  public int hashCode() {
    return Objects.hashCode(title);
  }

  @Override
  public String getTitle() {
    return title;
  }
}
```
</details>

</ul>
<li> 메뉴 그룹만들기</li>
<ul style="padding-left: 10px">
<li> 추상메서드 execute()를 구현하는 메서드가 필요하다.</li>
<ol style="padding-left: 10px">
<li> root menuGroup</li>
<ul style="padding-left: 10px">
<li> root는 parent가 필요없다.</li>
<li> 메뉴아이템의 값들을 List로 담을 addMenu()메서드가 필요하다.</li>
<li> 메뉴아이템의 값들을 List에서 제거할 removeMenu()메서드가 필요하다.</li>
<li> 메뉴아이템의 값들을 List에서 얻어올 getMenu()메서드가 필요하다.</li>
<li> 메뉴아이템의 갯수를 세는 countMenu()메서드가 필요하다.</li>
</ul>
<li> tree menuGroup</li>
<ul style="padding-left: 10px">
<li> tree는 parent가 필요하여 부모의 parent와 menuPath를 가져오는 메서드가 필요하다.</li>
<li> addMenu()에서 tree가 상위 그룹이면 setParent를 실행하는 로직을 추가한다.</li>
</ul>
</ol>
<details>
<summary> 코드 접기/펼치기</summary>

```java
import bitcamp.myapp.util.Prompt;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class MenuGroup extends AbstractMenu {
  private String exitMenuTitle = "이전";
  private MenuGroup parent;
  private Stack<String> menuPath;
  private List<Menu> children = new ArrayList<>();

  public MenuGroup(String title) {
    super(title);
    this.menuPath = new Stack<>();
  }

  public void setParent(MenuGroup parent) {
    this.parent = parent;
    this.menuPath = parent.menuPath;
  }

  public void setExitMenuTitle(String title) {
    exitMenuTitle = title;
  }

  @Override
  public void execute() {
    menuPath.push(title);
    printMenus();
    while (true) {
      String command = Prompt.input("%s>", getMenuPathTitle(menuPath));
      if (command.equals("menu")) {
        printMenus();
        continue;
      } else if (command.equals("0")) { // 이전 메뉴 선택
        menuPath.pop();
        return;
      }

      try {
        int menuNo = Integer.parseInt(command);
        Menu menu = getMenu(menuNo - 1);
        if (menu == null) {
          System.out.println("유효한 메뉴 번호가 아닙니다.");
          continue;
        }

        menu.execute();

      } catch (NumberFormatException ex) {
        System.out.println("숫자로 메뉴 번호를 입력하세요.");
      }
    }
  }

  private void printMenus() {
    System.out.printf("[%s]\n", title);
    int i = 1;
    for (Menu child : children) {
      System.out.printf("%d. %s\n", i++, child.getTitle());
    }
    System.out.printf("0. %s\n", exitMenuTitle);
  }

  private String getMenuPathTitle(Stack<String> menuPath) {
    StringBuilder strBuilder = new StringBuilder();
    for (String s : menuPath) {
      if (!strBuilder.isEmpty()) {
        strBuilder.append("/");
      }
      strBuilder.append(s);
    }
    return strBuilder.toString();
  }

  public void add(Menu child) {
    if (child instanceof MenuGroup) {
      ((MenuGroup) child).setParent(this);
    }
    children.add(child);
  }

  public void remove(Menu child) {
    children.remove(child);
  }

  public Menu getMenu(int index) {
    if (index < 0 || index >= children.size()) {
      return null;
    }
    return children.get(index);
  }

  public int countMenu() {
    return children.size();
  }
}
```
</details>
</ul>
<li> 메뉴 아이템만들기</li>
<ul style="padding-left: 10px">
<li> 메뉴아이템은 가장 마지막 메뉴이므로 command를 실행해야한다.</li>
<li> command인터페이스의 인스턴스를 받을 필드값이 필요하다.</li>
<li> 종류에 따른 command를 구현체를 설정하는 setCommand()가 필요하다.</li>
<li> 추상메서드 execute()를 구현하는 메서드가 필요하다.</li>
<details>
<summary> 코드 접기/펼치기</summary>

```java
import bitcamp.myapp.command.Command;

public class MenuItem extends AbstractMenu {
  Command command;

  public MenuItem(String title) {
    super(title);
  }

  public MenuItem(String title, Command command) {
    super(title);
    this.command = command;
  }

  @Override
  public void execute() {
    if (command != null) {
      command.execute(title);
    } else {
      System.out.println(title);
    }
  }
}

```
</details>
</ul>
</ol>

<h3 style="margin-left: 20px;"> 2) Command수정하기 </h3>
<ol>
<li> 커맨드 인터페이스 수정하기</li>
<ul style="padding-left: 10px">
<li> 커맨드 인터페이스의 execute()의 매개변수를 String 타입으로 수정한다.</li>

```java
public interface Command {

  void execute(String menuName);
}
```
</ul>

<li> 커맨드 추상클래스 삭제하기</li>
<ul style="padding-left: 10px">
<li> 커맨드의 추상클래스의 역할은 menu에서 수행 하므로 더이상 추상클래스가 필요없다.</li>
</ul>

<li> 각 커맨드 수정하기</li>
<ul style="padding-left: 10px">
<li> 커맨드가 필드값으로 메뉴값을 가질 필요가 없다. -> 필드 및 생성자 메뉴값 삭제</li>
<li> 커맨드가 메서드로 메뉴를 가져올 필요가 없다. -> getMenus() 삭제</li>
<details>
<summary> 코드 접기/펼치기</summary>

```java

public class BoardCommand implements Command {

  private List<Board> boardList;

  public BoardCommand(List<Board> list) {
    this.boardList = list;
  }

  @Override
  public void execute(String menuName) {
    System.out.printf("[%s]\n", menuName);
    switch (menuName) {
      case "등록":
        this.addBoard();
        break;
      case "조회":
        this.viewBoard();
        break;
      case "목록":
        this.listBoard();
        break;
      case "변경":
        this.updateBoard();
        break;
      case "삭제":
        this.deleteBoard();
        break;
    }
  }
  /* 이하 생략 */
}
```
</details>
</ol>

<h3 style="margin-left: 20px;"> 3) App수정하기 </h3>
<ol>
<li> 생성자 수정하기</li>
<ul style="padding-left: 10px">
<li> 기존 생성자 Map구조에서 ArrayList를 추가하는 생성자로 바꾼다.</li>
<details>
<summary> 코드 접기/펼치기</summary>

```java
  MenuGroup mainMenu = new MenuGroup("메인");
  UserCommand userCommand;
  BoardCommand boardCommand;
  ProjectCommand projectCommand;
  HelpCommand helpCommand;
  HistoryCommand historyCommand;

  public App() {
    List<User> userList = new ArrayList<>();
    List<Project> projectList = new LinkedList<>();
    List<Board> boardList = new LinkedList<>();

    userCommand = new UserCommand(userList);
    boardCommand = new BoardCommand(boardList);
    projectCommand = new ProjectCommand(projectList, userList);
    helpCommand = new HelpCommand();
    historyCommand = new HistoryCommand();

    MenuGroup userMenu = new MenuGroup("회원");
    userMenu.add(new MenuItem("등록", userCommand));
    userMenu.add(new MenuItem("목록", userCommand));
    userMenu.add(new MenuItem("조회", userCommand));
    userMenu.add(new MenuItem("변경", userCommand));
    userMenu.add(new MenuItem("삭제", userCommand));
    mainMenu.add(userMenu);

    MenuGroup projectMenu = new MenuGroup("프로젝트");
    projectMenu.add(new MenuItem("등록", projectCommand));
    projectMenu.add(new MenuItem("목록", projectCommand));
    projectMenu.add(new MenuItem("조회", projectCommand));
    projectMenu.add(new MenuItem("변경", projectCommand));
    projectMenu.add(new MenuItem("삭제", projectCommand));
    mainMenu.add(projectMenu);

    MenuGroup boardMenu = new MenuGroup("게시판");
    boardMenu.add(new MenuItem("등록", boardCommand));
    boardMenu.add(new MenuItem("목록", boardCommand));
    boardMenu.add(new MenuItem("조회", boardCommand));
    boardMenu.add(new MenuItem("변경", boardCommand));
    boardMenu.add(new MenuItem("삭제", boardCommand));
    mainMenu.add(boardMenu);
    mainMenu.add(new MenuItem("도움말", helpCommand));
    mainMenu.add(new MenuItem("명령내역", historyCommand));

    mainMenu.setExitMenuTitle("종료");
  }
```
</details>
</ul>

<li> execute()수정하기</li>
<ul style="padding-left: 10px">
<li> execute()의 기능은 menu로 이관되어 더이상 필요없다.</li>

```java
void execute() {
    try {
      mainMenu.execute();
    } catch (NumberFormatException ex) {
      System.out.println("숫자로 메뉴 번호를 입력하세요.");
    }

    System.out.println("종료합니다.");
    Prompt.close();
  }
```

</ul>
<li> 기타 필요없는 메서드 삭제</li>
<ul style="padding-left: 10px">
<li> 기타 메뉴 출력 관련 메서드는 menu로 이관되어 삭제한다.</li>
</ul>
</ol>
<h2 style="margin-left: 10px;"> 4.결과 확인하기 </h2>
<ul>
<li> Menu그룹을 나눔으로써 메뉴의 기능과 커맨드의 기능을 분리 하였다.</li>
<li> Menu를 출력하는 UI는 Menu에서 실행되어 콘솔창에 출력되고, tree노드에 접근 할때 까지 Menu에서 실행된다.</li>
<li> tree에 접근하면 커맨드 기능이 실행되어 결과값들을 담고, 다시 Menu로 넘어간다.</li>
</ul>

<h1> Command 복제 패턴</h1>
<h2 style="margin-left: 10px;"> 1. 복제패턴의 필요성 </h2>

```java
MenuGroup userMenu = new MenuGroup("회원");
    userMenu.add(new MenuItem("등록", userCommand));
    userMenu.add(new MenuItem("목록", userCommand));
    userMenu.add(new MenuItem("조회", userCommand));
    userMenu.add(new MenuItem("변경", userCommand));
    userMenu.add(new MenuItem("삭제", userCommand));
    mainMenu.add(userMenu);
```
<ul>
<li>코드를 보면 모두 같은 command를 사용하고 있다.</li>
<li>등록에서는 command에서 add기능만 필요로 하기 때문에 command의 전체를 호출하는 것은 비 효율적이다.</li>
<li>MenuItem에 새로운 메뉴가 추가되면 userCommand 전체를 수정해야한다.</li>

![image](https://github.com/user-attachments/assets/08b180df-b7bd-4f0b-a99a-a31de4fd24a4)
</ul>

<h2 style="margin-left: 10px;"> 2. 복제패턴의 구현 </h2>
<ul>
<li>기존의 메서드들을 각 클래스 파일로 분할 하면 된다.</li>

![image](https://github.com/user-attachments/assets/34c7c217-d740-483a-ab8d-22339fee6dae)

```java
public class UserAddCommand implements Command {
  private List<User> userList;

  public UserCommand(List<User> list) {
    this.userList = list;
  }

  @Override
  public void execute(String menuName) {
    User user = new User();
    user.setName(Prompt.input("이름?"));
    user.setEmail(Prompt.input("이메일?"));
    user.setPassword(Prompt.input("암호?"));
    user.setTel(Prompt.input("연락처?"));
    user.setNo(User.getNextSeqNo());
    userList.add(user);
  }
}

//동일 한 방법으로 클래스를 나눈다.

```

<li>기존의 App 클래스의 생성자는 다음과 같이 변경된다.</li>

```java
  public App() {
    List<User> userList = new ArrayList<>();
    List<Project> projectList = new LinkedList<>();
    List<Board> boardList = new LinkedList<>();

    MenuGroup userMenu = new MenuGroup("회원");
    userMenu.add(new MenuItem("등록", new UserAddCommand(userList)));
    userMenu.add(new MenuItem("목록", new UserListCommand(userList)));
    userMenu.add(new MenuItem("조회", new UserViewCommand(userList)));
    userMenu.add(new MenuItem("변경", new UserUpdateCommand(userList)));
    userMenu.add(new MenuItem("삭제", new UserDeleteCommand(userList)));
    mainMenu.add(userMenu);

    MenuGroup projectMenu = new MenuGroup("프로젝트");
    ProjectMemberHandler memberHandler = new ProjectMemberHandler(userList);
    projectMenu.add(new MenuItem("등록", new ProjectAddCommand(projectList, memberHandler)));
    projectMenu.add(new MenuItem("목록", new ProjectListCommand(projectList)));
    projectMenu.add(new MenuItem("조회", new ProjectViewCommand(projectList)));
    projectMenu.add(new MenuItem("변경", new ProjectUpdateCommand(projectList, memberHandler)));
    projectMenu.add(new MenuItem("삭제", new ProjectDeleteCommand(projectList)));
    mainMenu.add(projectMenu);

    MenuGroup boardMenu = new MenuGroup("게시판");
    boardMenu.add(new MenuItem("등록", new BoardAddCommand(boardList)));
    boardMenu.add(new MenuItem("목록", new BoardListCommand(boardList)));
    boardMenu.add(new MenuItem("조회", new BoardViewCommand(boardList)));
    boardMenu.add(new MenuItem("변경", new BoardUpdateCommand(boardList)));
    boardMenu.add(new MenuItem("삭제", new BoardDeleteCommand(boardList)));
    mainMenu.add(boardMenu);

    mainMenu.add(new MenuItem("도움말", new HelpCommand()));
    mainMenu.add(new MenuItem("명령내역", new HistoryCommand()));

    mainMenu.setExitMenuTitle("종료");
  }
```
</ul>
