# book_management
- 도서관에서 사용할 수 있는 도서관리 기능을 구현
- 도서 관리 시스템의 기본 기능을 제공하며, HTML, CSS, JavaScript를 사용하여 도서 목록을 추가,수정,삭제 관리
***
## 구현 화면
![도서관리](https://github.com/lcl3392/book_management/assets/133613544/a3771a13-afa7-46bd-8240-48b0e2714209)
***
## code 설명 
- (이 코드의 주요 기능은 도서 정보를 입력하고 출력하며, 도서 정보를 로컬 스토리지에 저장하고 삭제하는 것입니다.)
- 클래스 정의
 + Book 클래스: 도서 객체를 생성하는 클래스로, title, author, bookcode 속성을 가집니다.
 + BookUI 클래스: 도서 관련 기능을 담당하는 클래스로, 데이터 관리, 출력, 추가, 삭제, 메시지 표시 등을 처리합니다.
 ```
//클래스 
    class Book {
        constructor( title, author , bookcode ){
            this.title = title 
            this.author = author 
            this.bookcode = bookcode 
        }
    }
    class BookUI {
        getData(){
            data = JSON.parse(localStorage.getItem("data")) || []
        }
        showData(){
            console.log(data)
        data.map(item => {
            this.show(item)
            })      
        }
        //추가
        add( item ){
            const{title,author,bookcode} = item
            if( !title || !author || !bookcode ) return
            this.show(item) 
            //data에 추가 
            data = [...data, {title:title,author,bookcode}]
            localStorage.setItem("data", JSON.stringify(data))
        }
        //출력
        show(item){
            const{title,author,bookcode} = item
            const tr = document.createElement('tr');
            tr.innerHTML = `
                    <td>${title}</td>
                    <td>${author}</td>
                    <td>${bookcode} </td>
                    <td><button class="delete">X</button></td>
            `;
            $tbody.append( tr );
            this.reset()          
        }
        //삭제
        del( target ){          
            if (target.classList.contains('delete')) {                
                target.parentElement.parentElement.remove();
                this.showMessage('북리스트에 정상삭제완료' , 'on') 

                //코드 data = bookcode 삭제 
                let bookcode = target.parentElement.previousElementSibling.textContent.trim()
                
                console.log(bookcode)
                //삭제 filter
                data = data.filter(item => item.bookcode !== bookcode)
                localStorage.setItem("data", JSON.stringify(data))
            }
        }
        //메세지
        showMessage( msg , calssName ) {
            const div = document.createElement('div')
            div.textContent  = msg 
            div.className = `show ${calssName}` 
            $mainbook.append(div)

            //2초후 삭제 
            setTimeout( () => {
                document.querySelector('.show').remove();
            }, 1000 )
        }
        //텍스트지우기
        reset(){
            $title.value = ''
            $author.value = ''
            $bookcode.value = ''
            $title.focus()
        }
    }
```

- 데이터 관리
  + localStorage를 사용하여 도서 데이터를 브라우저 로컬 스토리지에 저장하고 불러옵니다.
  + 초기화 시, getData 메서드를 사용하여 저장된 데이터를 불러오고, showData 메서드를 사용하여 화면에 출력합니다.
  ```
  const bookUI = new BookUI()
    //화면에 출력
    bookUI.getData()
    bookUI.showData()
  ```
  
- 폼($form)에서 데이터를 제출할 때 발생하는 submit 이벤트를 처리
  + e.preventDefault();: 기본적으로 제출할 때 페이지를 새로고침하는 기본 동작을 막습니다.
  + 입력 필드에서 데이터 수집: 폼 내의 입력 필드에서 제목($title), 저자($author), 도서 코드($bookcode) 값을 가져옵니다.
  + 도서 객체 생성: 입력된 데이터를 사용하여 새로운 도서 객체를 생성합니다.
  + 데이터 유효성 검사: 제목, 저자, 도서 코드 중 하나라도 비어있는 경우 "값을 넣으세요" 메시지를 표시하고, 그렇지 않으면 도서를 추가합니다.
  + 데이터 추가: 유효한 데이터가 입력되면 도서를 목록에 추가하고 로컬 스토리지에 저장합니다.
  + 입력 폼 초기화: 입력 폼의 값을 초기화하고 제목 입력 필드로 포커스를 이동합니다.
  + 성공 메시지 표시: 도서가 성공적으로 추가되었음을 사용자에게 알리는 메시지를 표시합니다.
  ```
  $form.addEventListener('submit',e => {
        e.preventDefault();
        
        const title = $title.value 
        const author = $author.value 
        const bookcode = $bookcode.value 

        const book  = new Book( title , author , bookcode )
       

        if( !title || !author || !bookcode ) {
            bookUI.showMessage('값을 넣으세요' , 'off')
        }else {
            bookUI.add( book )
            bookUI.reset()      
            bookUI.showMessage('북리스트에 추가완료' , 'on')      
        }
    })
  ```
  
- 도서 삭제
  + 도서 목록에서 "X" 버튼을 클릭하면 해당 도서가 삭제됩니다.
  + del 메서드를 사용하여 도서를 삭제하고, 도서 목록과 localStorage에서 해당 도서를 제거합니다.
  ```
  $tbody.addEventListener('click', e => {
        const bookUI = new BookUI()
        bookUI.del( e.target )
    })

    localStorage.clear()
  ```
