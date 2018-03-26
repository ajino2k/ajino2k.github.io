## Wellcome Ajino2k
## 1 . Sự khác nhau giữa TRUNCATE và DELETE
Lệnh TRUNCATE và DELETE đều được dùng để xóa dữ liệu trong bảng. Cả hai lệnh này chỉ xóa dữ liệu mà không xóa cấu trúc bảng.

## 2. LệnhTRUNCATE
Lệnh TRUNCATE xóa tất cả dòng dữ liệu trong bảng mà KHÔNG ghi nhận lại trong transaction log. Lệnh TRUNCATE có cùng chức năng như lệnh DELETE là xóa dữ liệu trong bảng mà không làm thay đổi cấu trúc của bảng, tuy nhiên, bạn không dùng mệnh đề WHERE với lệnh TRUNCATE.

Cú pháp:

``TRUNCATE TABLE [{database_name.[schema_name].|schema_name.}]table_name``

Trong đó:

table_name: là tên của bảng cần xóa tất cả các dòng dữ liệu.
``VD: TRUNCATE TABLE Products;``

Lệnh trên sẽ xóa tất cả dữ liệu trong bảng Products.

## 3. Lệnh DELETE
Lệnh DELETE cũng xóa các dòng trong bảng, nhưng nó ghi lại các dòng được xóa trong transaction log. Bạn có thể dùng mệnh đề WHERE với lệnh DELETE để giới hạn các dòng được xóa.

Dưới đây là cú pháp đơn giản, thông tin chi tiết lệnh DELETE, xem link: DELETE (Transact-SQL).

Cú pháp:

``DELETE FROM TABLE_NAME [{database_name.[schema_name].|schema_name.}]table_name``
Trong đó:

database_name: Tên của cơ sở dữ liệu chứa bảng dữ liệu cần xóa. Đây là tùy chọn, nếu không chỉ định, cơ sở dữ liệu hiện tại được chọn.
schema_name: Tên của schema chứa bảng dữ liệu cần xóa. Đây là tùy chọn, nếu không chỉ định, schema hiện tại được chọn.
table_name: Tên của bảng dữ liệu cần xóa.
VD: DELETE FROM Products;

Lệnh trên sẽ xóa tất cả dữ liệu trong bảng Products.

Trong lệnh DELETE bạn có thể giới hạn số dòng dữ liệu được xóa bằng mệnh đề WHERE. Chỉ những dòng dữ liệu nào thỏa điều kiện trong mệnh đề WHERE mới được xóa, không phải tất cả dòng dữ liệu.

``VD: DELETE FROM Products WHERE ProductId IN (1, 2, 3);``

Lệnh trên chỉ xóa các dòng dữ liệu có ProductId là 1, 2, 3 trong bảng Products.

## 4. Khác nhau giữa TRUNCATE và DELETE
TRUNCATE và DELETE xóa dữ liệu nhưng không xóa cấu trúc

Cả hai lệnh đều xóa các dòng dữ liệu trong bảng, nhưng cấu trúc bảng, các cột, ràng buộc, chỉ mục,.. đều được giữ lại. Để xóa định nghĩa bảng và dữ liệu, dùng lệnh DROP TABLE.

Điều kiện trên tập dữ liệu xóa

Điều kiện trên tập dữ liệu xóa nghĩa là không phải tất cả dòng dữ liệu đều bị xóa. Nếu tôi có bảng Authors và tôi chỉ muốn xóa các Authors mà sống ở Australia.

``TRUNCATE`` – Lệnh TRUNCATE chúng ta sẽ không thể xóa theo điều kiện bởi vì lệnh này không cho phép mệnh đề WHERE.
``DELETE`` – Lệnh DELETE cho phép xóa dữ liệu theo điều kiện đi theo mệnh đề WHERE.
``DELETE`` và ``TRUNCATE`` đều ghi lại các hành động

Trong nhiều bài viết tôi đọc trên Internet, tôi thấy: ``“DELETE thì ghi nhận lại các hành động còn TRUNCATE thì không”``, nghĩa là khi thực hiện lệnh DELETE, nó sẽ ghi nhận lại thông tin các dòng bị xóa và khi thực hiện lệnh TRUNCATE nó sẽ không ghi lại gì cả. Nhưng thực tế không phải vậy, TRUNCATE cũng ghi lại hành động nhưng theo cách khác. Nó dùng ít tài nguyên hệ thống và transaction log hơn DELETE. Lệnh TRUNCATE ghi lại thông tin ở mức tối thiểu, đó là lý do tại sao nó nhanh hơn lệnh DELETE. Cả hai lệnh DELETE và TRUNCATE đều ghi lại các hành động, nhưng chúng làm việc khác nhau:

DELETE ghi lại hành động cho mỗi dòng. Lệnh DELETE xóa từng dòng một và ghi lại thay đổi trong transaction log cho mỗi dòng được xóa. Vì thế nếu xóa một lượng lớn các dòng dữ liệu thì có thể làm đầy transaction log. Điều này có nghĩa là khi xóa một lượng lớn các dòng dữ liệu sẽ dùng nhiều tài nguyên máy chủ cũng như ghi lại mỗi sự thay đổi cho mỗi dòng được xóa. Đó là lý do tại sao transaction log sẽ phình ra rất to. Một số người thắc mắc là tại sao Microsoft không sửa lệnh DELETE để không ghi lại mỗi dòng được xóa???. Câu trả lời là khi bạn chạy cơ sở dữ liệu trong chế độ khôi phục đầy đủ, thông tin chi tiết trong transaction log rất cần thiết để khôi phục cơ sở dữ liệu về trạng thái gần nhất.
TRUNCATE ghi lại các trang dữ liệu được hủy cấp phát khi bị xóa. Nó không ghi lại các dòng bị xóa, thay vào đó nó ghi lại các trang dữ liệu bị hủy cấp phát của bảng. Lệnh TRUNCATE xóa dữ liệu bằng cách hủy cấp phát các trang dữ liệu được dùng lưu trữ dữ liệu trong bảng và ghi lại chỉ các trang được hủy cấp phát trong transaction log. Thực sự, TRUNCATE không xóa dữ liệu, nó hủy cấp phát các trang dữ liệu và xóa con trỏ đến chỉ mục. Dữ liệu sẽ vẫn tồn tại cho đến khi nó bị ghi đè hoặc cơ sở dữ liệu shrunk. Hành động này không yêu cầu nhiều tài nguyên và vì thế rất nhanh. Có một lỗi phổ biến là nghĩ rằng truncate không ghi lại thao tác. Điều này sai, các trang dữ liệu bị hủy cấp phát được ghi nhận trong tập tin log. Vì thế, “Books Online (BOL)” ám chỉ hành động TRUNCATE là hành động “ghi nhật ký rất ít”. Bạn có thể dùng truncate đi kèm với transaction, và khi transaction được rolled-back, các trang dữ liệu được cấp phát lại lần nữa và cơ sở dữ liệu trở về trạng thái trước đó.
Để ví dụ cụ thể việc TRUNCATE cũng ghi lại thao tác:

``/*Create a dummy table in a non-production(dummy) database.*/
CREATE TABLE tranTest
(
    Id int Identity(1,1),
    Name Varchar(100)
)

/*Now insert the records in the tranTest table.*/
INSERT INTO tranTest(Name) VALUES('prashant')
INSERT INTO tranTest(Name) VALUES('prateek')
INSERT INTO tranTest(Name) VALUES('praveen')
INSERT INTO tranTest(Name) VALUES('prakash')
INSERT INTO tranTest(Name) VALUES('prabhat')

/*Then as you know delete is a looged operation that means 
  in a transaction if we rollback the transaction after deleting 
  the records from table, it will restore all deleted records.*/

BEGIN TRAN
    DELETE FROM tranTest
    SELECT * FROM tranTest
ROLLBACK

SELECT * FROM tranTest

/*Now delete all records from the table.*/
DELETE FROM tranTest

/*And Insert new fresh records in the table.*/
INSERT INTO tranTest(Name) VALUES('prashant')
INSERT INTO tranTest(Name) VALUES('prateek')
INSERT INTO tranTest(Name) VALUES('praveen')
INSERT INTO tranTest(Name) VALUES('prakash')
INSERT INTO tranTest(Name) VALUES('prabhat')

/*Then as you now you know that Truncate is also a logged 
  opertion so it must restore all records that are deleted from 
  the tranTest table in the below tansaction*/

BEGIN TRAN
    TRUNCATE TABLE tranTest
    SELECT * FROM tranTest
ROLLBACK

SELECT * FROM tranTest``

Khi chúng ta chạy lệnh trên và TRUNCATE cũng phục hồi tất cả dòng dữ liệu đồng nghĩa với TRUNCATE cũng ghi lại thao tác, nhưng dùng ít tài nguyên. Tôi nghĩ rằng dùng ít tài nguyên nghĩa là bạn có thể khôi phục lại lệnh truncte cho một phiên làm việc cụ thể nhưng nếu bạn đóng kết nối thì sẽ không thể phục hồi lại dữ liệu. Nhưng DELETE có thể phục hồi dữ liệu về sau.

## 5. Hành vi của DELETE và TRUNCATE đối với cột IDENTITY
OK, bây giờ trong trường hợp các cột Identity. Cả hai lệnh TRUNCATE và DELETE đều có sự khác nhau với các cột Identity. Khi dùng TRUNCATE, nó sẽ reset biến đếm được dùng bởi cột Identity cho các dòng dữ liệu mới. Nhưng trong trường hợp DELETE nó sẽ không reset biến đếm được dùng bởi cột Identity. Trong trường hợp muốn duy trì biến đếm cho cột Identity thì phải dùng DELETE thay vì TRUNCATE.

Tại sao có sự khác nhau này? Tôi không biết, nhưng T-SQL cung cấp cho bạn 2 cách để có thể dùng khi cần. Trong trường hợp bạn muốn tất cả dòng dữ liệu được xóa và biến đếm reset trở về 1, thì TRUNCATE là hợp lý. Nếu muốn xóa tất cả các dòng dữ liệu nhưng không muốn reset biến đếm, DELETE là hợp lý.

Ví dụ cho trường hợp này:

``INSERT INTO tranTest(Name) VALUES('prateek')
INSERT INTO tranTest(Name) VALUES('praveen')
INSERT INTO tranTest(Name) VALUES('prakash')
INSERT INTO tranTest(Name) VALUES('prabhat')

/* Now at this point the counter of this table's 
   identity column is 5 taht is the max value of id column */
SELECT * FROM tranTest

/* Then truncate the table. */
TRUNCATE TABLE tranTest

/* and insert new records */
INSERT INTO tranTest(Name) VALUES('prashant')
INSERT INTO tranTest(Name) VALUES('prateek')
INSERT INTO tranTest(Name) VALUES('praveen')
INSERT INTO tranTest(Name) VALUES('prakash')
INSERT INTO tranTest(Name) VALUES('prabhat')

/* Now you'll see that after truncating the table the 
   identity is reset to its seed value. */
SELECT * FROM tranTest

/* So this example explains the beahviour of both of these command for Identity columns. */``

## 6. TRUNCATE là lệnh DDL, trong khi DELETE là lệnh DML
Đây cũng là sự khác nhau cơ bản khi bạn đọc các bài viết. TRUNCATE là hành động DDL (data definition language) và DELETE là hành động DML (data manupulation language). Nhưng tại sao?

Khi chúng ta chạy lệnh TRUNCATE, nó đặt một “Schema modification (Sch-M)” lock trên bảng. “Schema modification (Sch-M)” là gì?.

Database Engine dùng schema modification (Sch-M) lock bảng trong suốt thời gian hành động DDL thực hiện, như add một cột hoặc drop 1 bảng. Trong suốt thời gian này, Sch-M ngăn cản các hành động truy xuất bảng. Có nghĩa là Sch-M khóa tất cả các hành động bên ngoài cho đến khi block được release. Bởi vì TRUNCATE không thực hiện bất kỳ hành động thay đổi dữ liệu nào trên bảng và là lý do DELETE TRIGGER không được gọi. Dù không thay đổi dữ liệu trên bảng, nhưng như bạn biết là TRUNCATE reset biến đếm của cột Identity, nghĩa là TRUNCATE thực hiện thay đổi định nghĩa bảng hay cấu trúc, chính là hành động DDL. Để được xếp vào hành động DDL, lệnh TRUNCATE phải thỏa:

Thay đổi cấu trúc hoặc định nghĩa bảng dưới dạng hành động DDL, và
Trong khi bạn thay đổi cấu trúc bảng, bạn không thể truy xuất bảng để thực hiện bất kỳ hành động thay đổi dữ liệu nào.
Với lệnh DELETE, tôi không chắc là có hành động lock, nhưng như đã biết lệnh DELETE xóa từng dòng một. Hành động thay đổi dữ liệu bằng cách xóa nó trong bảng, và bởi vì DELETE thực hiện thay đổi dữ liệu nên DELETE TRIGGER được gọi. Lệnh DELETE không thay đổi cấu trúc của bảng như cách mà lệnh TRUNCATE thay đổi reset biến đếm của cột Identity.

Để được xếp vào hành động DML, lệnh DELETE phải thỏa:

Thay đổi dữ liệu trong bảng.
Khi đang thay đổi dữ liệu trong bảng, bạn không thể thực hiện bất kỳ sự thay đổi cấu trúc bảng.
## 7. Hành vi TRUNCATE và DELETE đối với TRIGGER
Như bạn biết, trigger được gọi khi có bất kỳ sự thay đổi xảy ra trong bảng.

TRUNCATE – Khi bạn chạy lệnh TRUNCATE để xóa tất cả các dòng dữ liệu của bảng, nó thực sự không xóa dữ liệu mà chỉ hủy cấp phát các trang dữ liệu và vì thế trigger không được gọi.
DELETE – Trigger được gọi mỗi khi 1 dòng dữ liệu được xóa.
8. Chúng ta nên dùng lệnh nào trong trường hợp nào
Với DELETE

Lệnh DELETE có thể sinh lỗi nếu trigger cố gắng xóa dòng dữ liệu được tham chiếu bởi dữ liệu trong bảng khác có ràng buộc FOREIGN KEY. Nếu Delete xóa nhiều dòng, và 1 dòng không được xóa, lệnh sẽ bị hủy, lỗi sẽ được trả về và không dòng nào được xóa.
Với TRUNCATE

Bạn không thể dùng TRUNCATE TABLE trên bảng:

Được tham chiếu bởi ràng buộc FOREIGN KEY.
Trên một view được lập chỉ mục.
Được published dùng transactional replication hoặc merge replication.

## 9. Quyền thực hiện lệnh TRUNCATE và DELETE
Để TRUNCATE bảng, bạn cần tối thiểu là quyền ALTER trên bảng (được gán mặc định cho table owner, sysadmin, db_owner, và db_ddladmin). Để xóa dùng DELETE, chỉ cần cấp quyền DELETE.
