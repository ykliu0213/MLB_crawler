# MLB_crawler

**目標**：從 [`http://mlb.mlb.com/stats`](http://mlb.mlb.com/stats/sortable.jsp?fbclid=IwAR0I89OmWShmqR1pZGVJ5vugQIW5KHi0gH4Y0P3at44k4zW8OefYfrijDi0#elem=%5Bobject+Object%5D&tab_level=child&click_text=Sortable+Player+hitting&game_type='R'&season=2019&season_type=ANY&league_code='MLB'&sectionType=sp&statType=hitting&page=1&ts=1561611695464) 抓取所有打者和投手的數據並篩選出需要的標的

![](https://i.imgur.com/i1L7LK0.png)

## 流程
   1. 查看該網站開發工具中的network
    ![](https://i.imgur.com/pBZkBbb.png)

   2. 找出取得資料的xhr
    ![](https://i.imgur.com/eZ4s7bz.png)

   3. 使用GET請求來取得資料（JSON格式）
      ```python
        session = requests.session()
        # 設定請求訊息
        session.header = {'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64)'
                    'AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36'}
        
        # 透過輸入的網址送出請求，並取得內容
        def get_info(url):
            # 回傳的一般都是JSON格（字典）
            html = session.get(url)
            # 直接取出JSON格式
            return html.json()
      ```
   4. 解析JSON
      ```python
        # 透過一層一層解析到達球員數據層
        # 並查看第一位球員數據
        doc_json['stats_sortable_player']['queryResults']['row'][0]
      ```
   
   5. 展示所得到的球員資料  
        ![](https://i.imgur.com/Cx7Uwer.png)
   
   6. 將球員資料儲存成DataFrame
      ```python
        # 建立各list儲存各資料
        # 準備放入DataFrame中
        player_list = []
        ab_list = []
        obp_list = []
        slg_list = []
        ops_list = []

        # 將各球員資料儲存到list中
        def store_player_stats(index):
            player_list.append(player_stats_json[index]['name_display_last_init'])
            ab_list.append(player_stats_json[index]['ab'])
            obp_list.append(player_stats_json[index]['obp'])
            slg_list.append(player_stats_json[index]['slg'])
            ops_list.append(player_stats_json[index]['ops'])

        # 建立DataFrame
        players_dataframe = pd.DataFrame()

        # 將各個list放入dataframe對應項目
        players_dataframe['Player'] = player_list
        players_dataframe['AB'] = ab_list
        players_dataframe['OBP'] = obp_list
        players_dataframe['SLG'] = slg_list
        players_dataframe['OPS'] = ops_list
      ```
      **DataFrame如下：**  
      ![](https://i.imgur.com/zyhNeIq.png)
