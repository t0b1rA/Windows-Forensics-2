# Windows-Forensics-2
Thiết Bị lưu trữ


## I. The FAT file systems
### 1. Cluster
- **Khái niệm:**
        - CLuster là đơn vị lưu trữ nhỏ nhất trong hệ thống file FAT.
        - Khi một file được lưu trữ, nó không được lưu trong một khối duy nhất mà sẽ được chia thành các cluster.
        - Kích thước của cluster có thể thay đổi, thường từ 4KB đến 32KB (tùy vào hệ thống FAT)
- **Công dụng:**
        - Cluster giúp đơn giản hóa việc quản lí và định vị dữ liệu trên đĩa, vì thay vì xử lí từng byte hệ thống file chỉ cần làm việc với cluster.
        - Khi một file cần nhiều Cluster, hệ thống sẽ liên kết các cluster lại với nhau, tạo thành một chuỗi để lưu trữ dữ liệu của file đó.
- **Ý nghĩa trong Forensics:**
        - Slack space(phần dữ liệu không sử dụng hết trong cluster) có thể chứa dữ liệu còn sót lại sau khi file bị xóa, rất hữu ích trong việc phục hồi dữ liệu.
        - Khi phân tích ổ đĩa trong forensics, việc hiểu cách cluster hoạt động giúp xác định chính xác vị trí các file đã xóa hoặc bị ghi đè.
### 2. Directory
- **Khái niệm**
	- Directory là nơi chứa các metadata của file:
		- Tên file
	    - Kích thước file
	    - Thời gian tạo, sửa, truy cập (timestamp)
	    - Cluster bắt đầu (nơi lưu trữ dữ liệu của file).

- **Công dụng:**
	- Directory giúp quản lí và tổ chức các file trên ổ đĩa, mỗi file sẽ có một entry trong thư mục, lưu trữ thông tin để truy cập file dễ dàng.

- **Ý nghĩa trong forensics:**
	- Khi phân tích for, các directory entry có thể giúp khôi phục tên file và thông tin liên quan, ngay cả khi file đã bị xóa.
	- FAT directory thường có cấu trúc đơn giản, dẽ dàng để phân tích và phục hồi.
  
### 3. File Allocation Table(FAT)
- **Khái niệm:**
  	- FIle Allocation Table (FAT) là bảng quản lý tất cả các cluster trên ổ đĩa.
  	- FAT chứa danh sách liên kết (link list) của các cluster, giúp xác định địa chỉ của cluster tiếp theo trong chuỗi của file.
  	- Mỗi entry trong FAT tương ứng với một cluster trên đĩa và cho biết:
  	  - Trạng thái của cluster
  	  - CLuster tiếp theo trong chuỗi của file hoặc END of FILE (EOF) nếu đây là cluster cuối
  	  <img width="661" height="89" alt="image" src="https://github.com/user-attachments/assets/b0552ee4-59b2-4a8d-a066-1fc967d1a476" />
	  
- **Công dụng:**
- FAT giúp quản lý và điều phối các cluster trong việc lưu trữ và truy xuất dữ liệu, liên kết các cluster lại với nhau để tạo thành một file hoàn chỉnh.
- Nó giúp hệ thống biết file bắt đầu từ cluster nào, và các cluster tiếp theo nằm trong ổ đĩa.

- **Ý nghĩa trong Forensics:**
	- Khi một file bị xóa, FAT chỉ đơn giản là đánh dấu cluster là trống, nhưng dữ liệu của cluster vẫn còn. Điều này giúp phục hồi dữ liệu bằng cách tìm các chuỗi liên kết của các cluster.
	- FAT có thể giúp phân tích việc xóa file và khôi phục file trong quá trình điều tra số liệu.
### 4. Các loại FAT và công dụng của FAT
- **Khái niệm:**
	- FAT(File Allocation Table): hệ thống file FAT chia không gian đĩa thành các cluster để việc đánh địa chỉ (addressing) trở nên đơn giản hơn.
	- Số lượng cluster mà hệ thống có thể quản lí phụ thuộc vào số bit dùng để đánh địa chỉ cluster. Vì vậy mới có các phiên bản khác nhau của FAT.
	- Ban đầu FAT được phát triển với địa chỉ cluster 8-bit gọi là FAT-Structure.Sau này khi nhu cầu lưu trữ tăng lên thì các phiên bản FAT-12, FAT-16, FAT-32 xuất hiện
- **Chi tiết từng loại:**
	- FAT-12: là loại FAT sử dụng 12 bit để đánh địa chỉ các cluster giới hạn cluster của FAT-12 là (2^12) 4096 cluster. Thường được sử dụng trong các hệ thống cũ, với lưu trữ ít như thẻ nhớ nhỏ, đĩa mềm.
	- FAT-16:
		- Sử dụng 16 bit để đánh địa chỉ các cluster, giới hạn cho cluster của FAT-16 là (2^16) 65,536 cluster 
		- Ứng dụng: Được sử dụng rộng rãi trong các ổ đĩa cứng cũ hoặc thiết bị lưu trữ nhỏ.
	- FAT-32:
		- Sử dụng 28 bit (mặc dù tên gọi nó là 32), nó chỉ dùng 28 bit cho việc đánh địa chỉ cluster, và còn lại là dành cho flags và các giá trị hệ thống. đây là lý do mà FAT-32 không đạt được tối đa 4 tỷ cluster như 2^32.
		- Ưu điểm của FAT-32 là cho phép sử dụng ổ đĩa lớn hơn 2GB và hỗ trợ các file có kích thước lên đến 4GB.
		- Ứng dụng: Được sử dụng trên các thiết bị như SD, USB, ổ cứng ngoài.
	 - exFAT :
		- exFAT (Extended FAT) là phiên bản mở rộng của FAT-32, được phát triển cho các thiết bị lưu trữ di động và có dung lượng lớn như thẻ SD trên 32gb.
		- Công dụng: Hỗ trợ file lớn hơn 4gb và cải thiện hiệu suất với các file video và hình ảnh có dung lượng lớn.
		- Ứng dụng: Được sử dụng trên các thiết bị lưu trữ như USB, ổ cứng di động và thẻ SD lớn.
- **Ý nghĩa trong forensics các loại FAT:**
		- FAT-12. FAT-16, FAT-32 thường có cấu trúc đơn giản, dễ dàng phục hồi dữ liệu đã xóa bằng cách phân tích FAT và cluster chain.
		- exFATduf có khả năng hỗ trợ file lớn nhưng cấu trúc phức tạp hơn, đòi hỏi các công cụ forensics phức tạp hơn để phục hồi dữ liệu từ các cluster bị xóa.
## II. The NTFS file system

