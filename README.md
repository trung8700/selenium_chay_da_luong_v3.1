# selenium_chay_da_luong_v3.1
đã mở đc từng tab đa luồng xấp xếp cửa sổ theo thứ tự .
# selenium_chay_da_luong
import time
import concurrent.futures
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.keys import Keys

# Số lượng profile chrome portable cần chạy
num_profiles = 2 #int(input("nhập số lượng acc cần chạy: "))

# Số lượng profile chrome mỗi lần chạy
batch_size = 2 #int(input("nhập số acc mỗi lần chạy: "))
# Giá trị x trong phương thức driver.set_window_position(x, y)
current_window_position = -100
# Đường dẫn đến thư mục chứa các profile chrome portable
profiles_dir = 'E:/CHORMEPORTABLE'

profile_start = int(input("nhập acc muốn chạy: " ))
def run_on_browser(driver):
    # Mở trang web
    driver.get('https://opensea.io/collection/lens-protocol-profiles/activity')

    # Đợi trang web tải xong trước khi thực hiện các thao tác khác
    wait = WebDriverWait(driver, 10)

    # Tìm kiếm element
   
    kinh_lup = driver.find_element(
        By.XPATH, "/html/body/div[1]/div/div[1]/div/nav/ul/li/div/ul/li/div/button/i")
    kinh_lup.click()
    tim_kiem = wait.until(lambda driver: driver.find_element(By.XPATH, "/html/body/div[1]/div/div[1]/div/nav/div[2]/div[1]/div/div/div/div/input"))
    # Nhập vào element và gửi form
    tim_kiem.send_keys("trung", Keys.ENTER)
    time.sleep(3)
    ba_gach = driver.find_element(
        By.CSS_SELECTOR, "#__next > div > div.sc-aa344ffa-3.fWzEAV > div > nav > ul > li > div > ul > div.sc-29427738-0.dVNeWL > li > div > button > i")
    time.sleep(3)
    ba_gach.click()
   
    explore = driver.find_element(
        By.CSS_SELECTOR, "#__next > div > aside.sc-29427738-0.sc-3c846907-4.jbnCpt.eBdbmZ.sc-70c1abfb-0.hkormH > div > div > ul > li:nth-child(1) > div > button")
    time.sleep(3)
    explore.click()
    visual = driver.find_element(
        By.CSS_SELECTOR, "#__next > div > aside.sc-29427738-0.sc-3c846907-4.jbnCpt.eBdbmZ.sc-70c1abfb-0.hkormH > div > div > ul > li:nth-child(10) > a > div")
    time.sleep(3)
    visual.click()
    time.sleep(3)
    driver.get('https://twitter.com/')
# Vòng lặp qua từng nhóm profile chrome
for i in range(profile_start, num_profiles + profile_start , batch_size):
    # Mở 4 profile chrome từ i đến i + batch_size - 1
    # (vd: i = 0 thì mở profile chrome từ 1 đến 4)
    drivers = []
    for j in range(i, i + batch_size):
        # Tạo một trình duyệt mới với profile chrome portable thứ j
        chrome_options = webdriver.ChromeOptions()
        chrome_options.add_argument(
            f'user-data-dir={profiles_dir}/{j}/Data/profile')

        driver = webdriver.Chrome(executable_path=r'\chromedriver.exe', chrome_options=chrome_options)
        drivers.append(driver)
        # Mở cửa sổ với kích thước toàn màn hình
        #driver.maximize_window()
        #Thay đổi kích thước cửa sổ thành chiều rộngvà chiều cao 
        driver.set_window_size(200, 1100)
        # Tăng giá trị hiện tại của vị trí cửa sổ lên 100
        current_window_position += 100

        if current_window_position >=1000 :
             current_window_position = 0
        
        # Sắp xếp cửa sổ tại vị trí hiện tại
        driver.set_window_position(current_window_position, 0)
            
        # Tạo thread pool với 4 luồng và chạy hàm run_on_browser trên mỗi trình duyệt trong thread pool
    with concurrent.futures.ThreadPoolExecutor(max_workers=50) as executor:
        executor.map(run_on_browser, drivers)

        # Đóng tất cả các trình duyệt
time.sleep(5)
for driver in drivers:
    driver.quit()
    
