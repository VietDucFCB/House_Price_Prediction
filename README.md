
# Dự án Dự đoán Giá Nhà

**Mục tiêu:**  
Xây dựng quy trình hoàn chỉnh (end-to-end) để dự đoán giá nhà dựa trên dữ liệu thu thập từ trang web bất động sản. Dự án được chia thành nhiều bước, bao gồm cào dữ liệu (scraping), trích xuất đặc trưng từ mô hình LLM, làm sạch, và phân tích dữ liệu cũng như xây dựng mô hình dự đoán.

## Cấu trúc thư mục và mô tả các tệp tin chính

- **data_crawled_batdongsan/**: Thư mục chứa toàn bộ file .txt của từng trang đã được cào trên trang Bất động sản.  
- **codeCrawl.ipynb**: Notebook chứa logic cào dữ liệu từ trang web (sử dụng thư viện `requests`, `BeautifulSoup`, v.v.).  
- **raw_data.csv**: Toàn bộ dữ liệu thô được trích xuất và lưu lại sau khi cào (chưa làm sạch).  
- **implement_llm.ipynb**: Notebook triển khai mô hình LLM (Large Language Model) thông qua API (vd: Anyscale) để trích xuất đặc trưng từ dữ liệu văn bản.  
- **raw_data_llm.csv**: Lưu các đặc trưng do mô hình LLM trả về.  
- **final_raw_data.csv**: Tập dữ liệu cuối cùng sau khi hợp nhất `raw_data.csv` và `raw_data_llm.csv` (chưa qua các bước tiền xử lý khác).  
- **final_report.ipynb**: Notebook chính, vừa chứa code vừa đóng vai trò báo cáo. Trong đây sẽ thực hiện làm sạch, xây dựng mô hình, đánh giá kết quả, v.v.  
- **coordinates.csv**: Danh sách tọa độ (kinh độ, vĩ độ) các quận/huyện phục vụ quá trình mapping hoặc phân tích vị trí.  
- **quan_huyen.geojson**: File GeoJSON chứa tọa độ (đỉnh đa giác) ranh giới của quận/huyện, dùng để trực quan hóa trên bản đồ.  
- **EDA_TEST_ON_RAW_DATA.html**: Báo cáo EDA (phân tích dữ liệu thăm dò) trên dữ liệu thô ban đầu, thường là biểu đồ, bảng thống kê.  
- **EDA_TEST_ON_CLEANED_DATA.html**: Báo cáo EDA trên dữ liệu đã được làm sạch, giúp so sánh và đánh giá chất lượng tiền xử lý.

## Yêu cầu Hệ thống

- **Python 3.x**  
- **Các thư viện cần thiết** (cài đặt qua `pip install -r requirements.txt` hoặc thủ công):
  - `requests`
  - `beautifulsoup4`
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`
  - `scikit-learn`
  - `anyscale` (hoặc thư viện tương tự nếu dùng LLM)
  - `jupyter` hoặc `jupyterlab` để chạy notebook

## Quy trình:
### 1. Thu thập dữ liệu:
- Thu thập dữ liệu từ trang web batdongsan.vn và tổ chức thông tin chi tiết về bất động sản vào một tệp CSV để dễ dàng quản lý và phân tích.Tự động hóa quá trình thu thập và xử lý dữ liệu từ nhiều trang web bất động sản. Tạo ra một cơ sở dữ liệu dễ dàng truy cập và sử dụng để phân tích thị trường bất động sản. Tối ưu hóa thời gian và công sức so với việc thủ công thu thập và nhập liệu. 
- sử dụng Selenium (cụ thể là webdriver.Edge) để tự động điều khiển trình duyệt (Edge) nhằm thu thập dữ liệu từ trang web. Bên cạnh đó, bạn kết hợp với BeautifulSoup để phân tích (parse) nội dung HTML sau khi Selenium đã tải trang. Đây là cách tiếp cận “lai” giữa WebDriver và HTML parsing để cào dữ liệu.
- lưu dữ liệu thô dưới dạng file csv và thực hiện tiền xử lý đầu tiên.
### 2. Thực hiện trích xuất đặc trưng bằng LLM:
- Tự động trích xuất các đặc trưng quan trọng từ mô tả bất động sản bằng cách sử dụng API của Anyscale. Các đặc trưng này bao gồm giá, diện tích, địa chỉ, số phòng ngủ, số phòng tắm, số tầng, tiện ích, tên đường, tên phường, tên quận, số mặt tiền, và thông tin về vị trí nhà. Mã nguồn được thiết kế để tăng cường hiệu quả và độ chính xác của quá trình phân tích dữ liệu bất động sản thông qua việc tự động hóa việc trích xuất đặc trưng từ các văn bản mô tả.
- Sử dụng biến môi trường để lấy API base URL và token truy cập Anyscale API. Quá trình chuẩn bị dữ liệu bao gồm tạo và cấu trúc hóa DataFrame chứa mô tả bất động sản để sẵn sàng cho việc trích xuất đặc trưng.
- Đoạn prompt là một chuỗi định dạng dài, được sử dụng để tạo yêu cầu gửi đến API của Anyscale để trích xuất các đặc trưng từ văn bản mô tả bất động sản. Đây là một mẫu minh họa rõ ràng cho API về những gì nhóm muốn trích xuất từ văn bản tiếng Việt. Đoạn prompt này không chỉ giúp cho API hiểu rõ yêu cầu của bạn mà còn cung cấp cho người dùng và nhà phát triển một hình dung rõ ràng về những gì được trích xuất từ dữ liệu đầu vào. Việc sử dụng prompt giúp tăng tính mạch lạc và đúng đắn trong quá trình gửi yêu cầu và xử lý kết quả từ Anyscale API.
- Thiết kế để xử lý lượng lớn dữ liệu, chia thành các batch để xử lý một cách hiệu quả. Kết quả từ mỗi batch được lưu trữ vào một DataFrame và sau đó được xuất ra tệp CSV để dễ dàng quản lý và phân tích sau này.
### 3. Tiền xử lý dữ liệu và Kỹ thuật xây dựng đặc trưng:
- Sau khi đã cào được dữ liệu từ website và sử dụng API của Anyscale để lấy thêm 1 vài đặc trưng liên quan đến giá nhà, tiếp đến là thực hiện gom 2 bộ dữ liệu thô thành 1 tập dữ liệu thô duy nhất có đầy đủ đặc trưng cần thiết cho mô hình
- Mục tiêu là hợp nhất hai DataFrame df1 và df2 dựa trên cột Listing ID và xử lý các giá trị thiếu hoặc không hợp lệ trong các cột Price, Area, Bedrooms, và Bathrooms bằng cách sử dụng giá trị từ DataFrame còn lại.
- Trong quá trình xử lý và phân tích dữ liệu bất động sản, việc làm sạch và chuyển đổi dữ liệu là một bước quan trọng để đảm bảo chất lượng và tính chính xác của dữ liệu đầu vào cho mô hình dự đoán. Dưới đây mô tả các bước làm sạch dữ liệu từ hai DataFrame và áp dụng các hàm để xử lý các giá trị thiếu hoặc không hợp lệ trong các cột Price, Area, Bedrooms, Bathrooms, Amenities, Floors, Main road, và Frontages.
- Lấy tọa độ địa lý (kinh độ và vĩ độ) của các quận huyện từ một API không đồng bộ và tạo DataFrame chứa thông tin này
- Sử dụng thư viện aiohttp để thực hiện các yêu cầu không đồng bộ tới API để lấy tọa độ của các quận huyện
- Định nghĩa hàm get_coordinates để gửi yêu cầu HTTP và lấy kết quả từ API cho mỗi quận huyện
- Sử dụng hàm fetch_all_coordinates để gửi các yêu cầu không đồng bộ cho tất cả các quận huyện
- Chạy vòng lặp để gọi hàm fetch_all_coordinates cho mỗi quận huyện và chờ đợi kết quả
- **Điểm mạnh**: khi sử dụng API của geocode có thể lấy gần như chính xác tạo độ của các quận huyện tại Hồ Chí Minh mà có thể sử dụng miễn phí
- **Điểm yếu**: sẽ có 1 vài trường hợp cho thông tin bị sai lệch nếu như cung cấp địa chỉ chưa được rõ và tuyệt đối và khi sử dụng gói miễn phí thì chỉ được 1 request trên 1 giây
DataFrame coordinates_df chứa thông tin về tọa độ địa lý của các quận huyện, bao gồm tên quận huyện, vĩ độ và kinh độ
- Loại bỏ các outlier bằng kĩ thuật Interquartile Range (IQR), Kĩ thuật này thường được sử dụng để xác định các điểm dữ liệu không bình thường dựa trên phân phối của dữ liệu. phương pháp này sử dụng phân vị 25% (Q1) và phân vị 75% (Q3) của dữ liệu để tính toán khoảng IQR (Interquartile Range), là sự chênh lệch giữa Q3 và Q1. Sau đó, các giá trị ngoại lai thường được xác định dựa trên khoảng IQR bằng cách xác định các giá trị nằm ngoài phạm vi giới hạn dưới (lower_bound = Q1 - 1.5 * IQR) và giới hạn trên (upper_bound = Q3 + 1.5 * IQR). Các giá trị ngoại lai được xác định dựa trên phương pháp này sau đó được loại bỏ khỏi tập dữ liệu.

### 4. Xây dựng các mô hình dự đoán 
- xây dựng 4 mô hình (bao gồm: Linear Regression Model, Ridge Regression Model, Decision Tree Model và Gradient Boosting Model) và để đánh giá các mô hình trên chúng em sử dụng các Chỉ số hồi quy (MSE, MAE) và R²
![image](https://github.com/user-attachments/assets/72a6e174-7428-4f6f-864c-202448959d91)
- Mô hình Linear Regression có MSE và MAE cao, cùng với R² thấp nhất (0.457208) trong các mô hình được so sánh. Điều này cho thấy mô hình này chưa tốt trong việc dự đoán giá trị mục tiêu (Price) từ các đặc trưng (Area, Bedrooms, Bathrooms). Mô hình này đơn giản và dễ hiểu nhưng không đủ mạnh để bắt được các mối quan hệ phức tạp trong dữ liệu.
- Mô hình Ridge Regression có kết quả tương tự với Linear Regression, chỉ khác biệt rất nhỏ về MSE, MAE và R². Điều này cho thấy Ridge Regression không cải thiện đáng kể so với Linear Regression, có thể do các đặc trưng không có nhiều multicollinearity mạnh (theo ma trận tương quan, không có giá trị nào quá cao).
- Mô hình Decision Tree Regressor cải thiện so với Linear và Ridge Regression với MSE và MAE thấp hơn, cùng với R² cao hơn (0.466256). Tuy nhiên, giá trị cải thiện không quá lớn. Mô hình này có khả năng bắt các mối quan hệ phi tuyến tính tốt hơn nhưng có thể bị overfitting nếu không được điều chỉnh cẩn thận.
- Mô hình Gradient Boosting Regressor có kết quả tốt nhất trong các mô hình với MSE và MAE thấp nhất và R² cao nhất (0.503302). Mô hình này có khả năng kết hợp nhiều cây quyết định để tạo ra dự đoán tốt hơn, bắt được các mối quan hệ phức tạp hơn trong dữ liệu.

### 5. Kết luận:
- Có rất nhiều yếu tố ảnh hưởng đến giá của một căn nhà trong thực tế nhưng trong bộ dữ liệu đã thu thập được trên web thì chỉ lấy được các yếu tố ảnh hưởng đặc biệt là Diện tích, số phòng ngủ và số phòng tắm ngoài ra các yếu tố khác không có tác động nhiều. Có thể 1 phần là do việc xử lý dữ liệu chưa được phù hợp với bộ dữ liệu này hoặc do cách lấy dữ liệu từ việc cào cho đến sử dụng LLM để phân tích lấy ra các đặc trưng, cũng có thể là do người bán đã đăng thông tin chưa được chính xác dẫn đến việc lấy thông tin nhiễu, sai lệch làm cho không thể xây dưng mô hình với nhiều đặc trưng với độ tương quan mạnh
- Các mô hình hồi quy tuyến tính và học máy khác đã được xây dựng và đánh giá để dự đoán giá nhà dựa trên các đặc trưng đã chọn. Tuy nhiên, kết quả đánh giá cho thấy hiệu suất của các mô hình chỉ đạt mức trung bình, cho thấy còn nhiều cơ hội để cải thiện độ chính xác dự đoán.
- Cung cấp một phần những hiểu biết hữu ích về thị trường bất động sản và có thể đóng góp một phần vào việc hỗ trợ các quyết định đầu tư, định giá bất động sản và xây dựng các chiến lược kinh doanh. Tuy nhiên, cần lưu ý rằng mô hình dự đoán chỉ là một công cụ hỗ trợ và không thể thay thế hoàn toàn đánh giá của chuyên gia.
