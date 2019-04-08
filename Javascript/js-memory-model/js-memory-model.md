# JavaScript’s Memory Model

Với 1 progmamer, việc khai báo biến, khởi tạo giá trị cho biến như là công việc hằng ngày ở huyện.
```js
    // declare some variables and initialize them
    var a = 5
    let b = 'xy'
    const c = true

    // assign new values
    a = 6
    b = b + 'z'
    c = false // TypeError: Assignment to constant variable 
```
Nhưng điều gì **_thực sự_** xảy ra khi chúng ta làm điều đó? Javascript thực thi như thế nào? Chúng ta liệu có biết được những gì xảy ra ở phía dưới trong javascript không?, chúng ta thường hay bỏ qua những điều đó hêt sức căn bản đó, cho tới khi gặp phải vấn đề thì mới biết tầm quan trọng của nó.

#### Tôi sẽ trình bày theo các ý sau:
1. Khai báo biến và gán value trong JS primitive (Kiểu dữ liệu nguyên thủy).
2. Mô hình JavaScript’s memory: call stack và heap.
3. Khai báo biến và gán value trong JS non-primitives.
4. Let vs const.
### Khai báo biến và gán value trong JS primitive.
Hãy bắt đầu với ví dụ đơn giản sau:
```js
    let myNumber = 23;
```
Code sẽ được thực thi như sau:
1. Tạo 1 unique identifier cho biến `myNumber`.
2. Cấp phát 1 ô địa chỉ trong bộ nhớ (sẽ được tạo ra ở thời điểm thực thi).
3. Lưu trữ value vào ô địa chỉ được cung cấp (23).

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/1.jpeg)

Chúng ta hay nói theo cách thông thường là `giá trị của myNumber bằng 23`, nói theo technical thì sẽ là `giá trị của myNumber bằng giá trị của địa chỉ ô nhớ đang lưu trữ là 23`, đó mới là cách hiểu đúng.

Bây giờ tạo 1 biến mới có tên là `newVar` và gán `myNumber` cho nó:
```js
    let newVar = myNumber;
```
Địa chỉ trên ô nhớ của `myNumber` là `0012CCGWH80`, của `newVar` cũng là `0012CCGWH80` và cùng có giá trị được lưu trữ là `23`
Người ta hay quen miệng nói rằng `giá trị của newVar bây giờ là 23` =)).
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/2.jpeg)

Điều gì sẽ xảy ra nếu cộng thêm 1:
```js
    myNumber = myNumber + 1
```
Gía trị của `myNumber` chắc chắn là `24`, nhưng liệu `newVar` sẽ có giá trị là `24` hay không?, mặc dù 2 thằng đó trỏ tới cùng địa chỉ ô nhớ =)).

Câu trả lời sẽ là `không`, bởi vì trong js kiểu primitive data thuộc dạng `immutable`, nếu chưa biết thì xem tạm ở [link này](https://kipalog.com/posts/Ban-da-thuc-su-hieu-mutable-va-immutable), vài hôm nữa viết sau. :v. Khi `myNumber + 1` = `24`, thì Js sẽ cấp 1 địa chỉ mới trong bộ nhớ để lưu trữ giá trị `24`, lúc này `myNumber` sẽ trỏ tới 1 ô địa chỉ mới:
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/3.jpeg)

##### Một ví dụ khác:
```js
    let myString = 'abc';
    myString = myString + 'd';
```
Người mới học js có thể nói rằng kí tự `d` đơn giản là được thêm vào cuối của chuỗi `abc` và nó nằm trên cùng ô địa chỉ trong bộ nhớ, điều này là sai hoàn toàn. Trong js, string cũng là `primitive data`, thì nó cũng giống bên trên thôi:
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/4.jpeg)

#### Tiếp theo, chúng ta sẽ tiếp tục tìm hiểu lưu trữ data trong `call stack` và `heap` như thế nào.
Trong phạm vi bài viết này, JS memory model có thể được hiểu là có 2 khu vực riêng biệt là `call stack` && `heap`.
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/5.jpeg)

`Call Stack` là nơi lưu trữ dạng primitives (với điều kiện là đã gọi function). Trong hình minh họa sau, tôi đã mô phỏng cách mà địa chỉ bộ nhớ hiển thị giá trị của mỗi biến. Nhưng trong thực tế thì đừng quên rằng biến sẽ trỏ tới địa chỉ trong bộ nhớ, từng địa chỉ sẽ lưu trữ giá trị của từng biến.
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/6.jpeg)

`Heap` là nới lưu trữ dạng `non-primitives`, điều khác biệt ở đầy là heap có thể lưu trữ dữ liệu lộn xộn, rất phù hợp cho loại kiểu dữ liệu mà giá trị của chúng lên xuống như tính tình của các em gái đến ngày vậy: `array`, `object`.


### Khai báo biến và gán value trong JS non-primitives.

Kiểu data Non-primitive sẽ có hoạt động khác so với kiểu primitive.
Cùng nhau xem qua ví dụ sau với việc khai báo 1 biến tên là `myArray` và gán mảng rỗng cho nó.
```js
    let myArray = [];
```
Câu chuyện lại bắt đầu như sau:
1. Tạo 1 `unique identifier` cho biến `myArray`.
2. Cấp phát 1 địa chỉ trong bộ nhớ.
3. Lưu trữ value của ô dịa chỉ được cấp phát vào trong `heap` (will be assigned at runtime).
4. Ô địa chỉ ở trong heap sẽ chứa value được gán `(mảng rỗng[])`.

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/7.jpeg)

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/8.jpeg)

Từ đó chúng ta có thể push, pop phần tử hoặc làm bất cứ điều gì cũng được.

```js
    myArray.push("first")
    myArray.push("second")
    myArray.push("third")
    myArray.push("fourth")
    myArray.pop()
```
![JavaScript’s Memory Model](/Javascript/js-memory-model/images/9.jpeg)


#### Bây giờ mới là phần chính: let vs const.

Thông thường chúng ta hay sử dụng `const` để khai báo biển và chỉ sử dụng `let` khi chúng ta biết rằng giá trị của biến đó sẽ được `thay đổi`.

Liệu chúng ta có thực sự hiểu rõ về ý của từ `thay đổi` ở đây không.

Điều nhầm lẫn chính là việc giải thích nghĩa của từ `thay đổi` ở đây là `thay đổi giá trị`, bạn sẽ viết code như này phải không:

```js
    let sum = 0
    sum = 1 + 2 + 3 + 4 + 5
    let numbers = []
    numbers.push(1)
    numbers.push(2)
    numbers.push(3)
    numbers.push(4)
    numbers.push(5)
```

Hoàn toàn đúng khi dùng `let` để khai báo `sum` bởi vì chúng ta biết giá trị của biến sẽ thay đổi. Tuy nhiên, sử dụng `let` để khai báo cho `numbers` là không hợp lý lắm, bởi vì chúng ta nghĩ rằng khi thêm phần tử vào array thì value array đó sẽ thay đổi.

**Cách giải thích chính xác cho cái gọi là bị `thay đổi` ở đây là `thay đổi địa chỉ trong bộ nhớ`**. `let` cho phép bạn thay đổi địa chỉ trong bộ nhớ, còn `const` thì không:
```js
    const importantID = 489
    importantID = 100 // TypeError: Assignment to constant variable
```
Chúng ta hãy đặt tay lên cằm và trầm tư 1 lúc:
Khi khai báo `importantID` thì 1 ô địa chỉ sẽ được cấp phát và value của nó được lưu trữ là `489`:

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/10.jpeg)

Khi gán lại 100 cho importantID thì 1 ô nhớ mới được tạo ra bởi vì 100 là `primitive`, 100 sẽ được lưu trữ trong ô nhớ đó. Lúc này Js cố gắng tạo ra 1 ô địa chỉ mới cho `importantID`, điều này sẽ gây ra lỗi. Nó hoàn toàn đúng theo lý thuyết khoa học là: giá trị của hằng số thì không thay đổi được, giống như trong học toán vậy.

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/11.jpeg)

Hồi mới học Js, cứ theo lý thuyết là khai báo `const` cho biến mà giá trị luôn cố định, còn dùng `let` cho biến mà value bị reassign cho chắc cốp. Đối với `array` thì cứ dùng `let` mà chơi bởi vì nghĩ rằng khi dùng `const` thì không thể thêm phần tử vào array được, nhiều lúc chơi cần hơi quá, cứ dùng const cho array, rồi push phần tử vào mà chả thấy lỗi gì =))), ngáo rồi thấy code không lỗi thì cũng không tìm hiểu tại sao nữa, mặc dù nó confuse với lý thuyết kia, đến khi đọc bài này mới hiểu là tại sao.

Bây giờ, thì cũng tìm hiểu tiếp nhé.

```js
    const myArray = [];
```

Khi myArray được khai báo, 1 địa chỉ bộ nhớ được tạo ra trên `call stack`, `value của địa chỉ bộ nhớ trên stack` lúc này là `địa chỉ bộ nhớ được tạo ra` trên `heap`, giá trị được lưu trữ trên heap lúc này thực ra là 1 mảng rỗng, minh họa qua hình dưới đây:

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/12.jpeg)

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/13.jpeg)

Khi bạn thêm cái chai, cái lọ vào:

```js
    myArray.push(1)
    myArray.push(2)
    myArray.push(3)
    myArray.push(4)
    myArray.push(5)
```

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/14.jpeg)

Ở đây chúng ta push phần tử vào cái mảng rỗng đã tồn tại ở trên `heap`, tuy nhiên **địa chỉ bộ nhớ của `myArray` thì không bị thay đổi**. Mặc dù myArray được khai báo với const, js không phun ra lỗi. `myArray` có địa chỉ bộ nhớ và value trên `stack` vẫn là `0458AFCZX91`, `22VVCX011`, và địa chỉ bộ nhớ trên `heap` vẫn là `22VVCX011`.

Khi chúng ta làm thế này thì sao:
```js
    myArray = 3
```

Bởi vi 3 là dạng `primitive`, 1 địa chỉ bộ nhớ mới trên call stack `sẽ được tạo ra` và 3 sẽ được lưu trữ trên đó và sau đó được `assign` cho myArray, nhưng bởi vì `myArray` được khai báo với `const`, vì thế nó sẽ không xảy ra.

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/15.jpeg)

Tiếp theo 1 ví dụ nữa:

```js
    myArray = ['a']
```

['a'] là `new non-primitive array`, 1 địa chỉ bộ nhớ mới trên call stack sẽ được tạo ra, value của địa chỉ ô nhớ trên stack sẽ làm địa chỉ ô nhớ trên heap và value của địa chỉ bộ nhớ trên heap lúc này là ['a'], mà myArray được khai báo với `const`, vì thế lỗi sẽ xuất hiện:

![JavaScript’s Memory Model](/Javascript/js-memory-model/images/16.jpeg)

Với `object`, khi khai báo bằng `const` thì sao? Nó giống như array, thuộc dạng `non-primitive`, bạn có thể: `add key, update value, ...` mà không sợ bị lỗi.
```js
    const myObj = {}
    myObj['newKey'] = 'someValue' // this will not throw an error
```

### Tại sao biết được những điều căn bản này lại có ích cho chúng ta.

Theo thống kê của [Github](https://github.blog/2018-11-15-state-of-the-octoverse-top-programming-languages/) và [Stack Overflow’s Annual Developer Survey](https://insights.stackoverflow.com/survey/2018/) thì js là ngôn ngữ lập trình đứng top đầu được sử dụng trong năm 2018.

Các khóa học online và sách dạy lập trình khuyên chúng ta dùng `const`, `let` thay cho `var`, nhưng họ lại không nói là tại sao. Điều đó không giúp người học thực sự biết tại sao nhiều khi dùng `const` bị lỗi khi thay đổi `value`, nhiều lúc thì không. Nó giúp tôi hiểu ra rằng, nhiều programer dùng `let` để khai báo mặc định ở tất cả mọi nơi, để tránh rắc rồi.

Tuy nhiên điều này không được khuyến khích. Google là tổ chức mà số lượng code họ viết ra nhiều nhất trên thế giới, có sử dụng bộ quy ước `Javascript style guide` của riêng họ: `sử dụng const hoặc let cho tất cả các biến local. Mặc định dùng const để khai báo biến, dùng let cho biến mà value bị reassign, không bao giờ được dùng var` ([nguồn](https://google.github.io/styleguide/jsguide.html#features-use-const-and-let)).

Mặc dù họ không nói rõ rằng là tại sao, nhưng theo tôi thì đây là vài nguyên nhân:
1. Hạn chế bugs không kiểm soát được.
2. Biến được khai báo với `const` phải được khởi tạo khi khai báo, điều nay buộc coder phải khai báo biến 1 cách cẩn thận trong phạm vi hoạt động scope. Điều này tốt cho quản lý memory và performance.
3. Để code của bạn clean hơn, nhìn vào khai báo biến là biết được cái nào là `immutable`, cái nào là `reassign`.

##### Dịch từ: [JavaScript’s Memory Model](https://medium.com/@ethannam/javascripts-memory-model-7c972cd2c239?fbclid=IwAR06-NJL8snvK54HEbu_OiHxD5A11jjeSOSr2f3MrXaEjUywmqA63Z9eT_s)

#### Tham khảo thêm:
1. [Google JS Style Guide](https://google.github.io/styleguide/jsguide.html).
2. [Learning JavaScript: Call By Sharing, Parameter Passing](https://blog.bitsrc.io/master-javascript-call-by-sharing-parameter-passing-7049d65163ed).
3. [How JavaScript works: memory management + how to handle 4 common memory leaks](https://blog.sessionstack.com/how-javascript-works-memory-management-how-to-handle-4-common-memory-leaks-3f28b94cfbec).
