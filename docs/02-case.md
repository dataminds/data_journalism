# 정당강령 비교 사례 {#case}

국민의힘과 더불어민주당 홈페이지에 게재된 정당 강령을 스크랩해  두 문서에 사용된 단어의 빈도를 계산하는 작업. 총빈도, 상대빈도, 감정어 빈도를 계산해 의미를 파악한다. 

R을 10시간 정도 학습하면 가능한 작업이다. 만들어 놓은 코드를 재활용하면 1시간 정도에 작업을 마무리할 수 있다.  즉, 1시간 정도면 두세꼭지의 기사를 쓸수 있는 거리가 생긴다. 


## 패키지


```r
c(
  "tidyverse", # 어지러웠던 R세상을 깔끔하게 정돈. 
# "dplyr",     # tidyverse 부착. 공구상자
# "stringr",   # tidyverse 부착. 문자형자료 처리
  "tidytable", # datatable패키지를 tidy인터페이스로. 빠르고 함수명 혼란 제거
  "rvest",     # 웹 스크래핑 도구
  "janitor",   # 정제 및 기술통계 도구
  "tidytext",  # 텍스트를 깔끔하게 정돈하는 도구
  "RcppMeCab", # 형태소분석기
  "tidylo",    # 상대빈도 분석
  "gt"         # tidy원리를 적용한 표 생성. 
  ) -> pkg 
```





```r
sapply(pkg, function(x){
  if(!require(x, ch = T)) install.packages(x, dependencies = T)
})
```


```r
sapply(pkg, require, ch = T)
```

```
## tidyverse tidytable     rvest   janitor  tidytext RcppMeCab    tidylo        gt 
##      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE      TRUE
```


## 자료

### 본문 스크랩

`rvest`패키지로 웹사이트에서 자료 스크랩

- 국민의당

```r
# 코드만 표시
url <- "https://www.peoplepowerparty.kr/renewal/about/preamble.do"
text_css <- "#wrap > div.content-area.about.preamble > div > div.page-content > div.content-box > div > div.line-txt-box > div"

url %>% read_html() %>% 
  html_node(css = text_css) %>% 
  html_text() -> ppp_v
saveRDS(ppp_v, "ppp_v.rds")
```

- 더불어민주당

```r
# 코드만 표시
url <- "https://theminjoo.kr/introduce/rule/doct"
text_css <- "#content > div.minjoo_rule.tap_wrap > div.rule_cnt.tap_cnt_box.container_t > div > dl"

url %>% read_html() %>% 
  html_node(css = text_css) %>% 
  html_text() -> tmj_v
saveRDS(tmj_v, "tmj_v.rds")
```

`tidytext`패키지와 `RcppMeCab`패키지를 이용해 품사를 기준으로 토큰화해 데이터프레임으로 저장. 셀 하나당 토큰 하나. 


```r
readRDS("ppp_v.rds") %>% 
  tibble(text = .) %>% 
  # 품사로 토큰화
  unnest_tokens(output = word, input = text, token  = pos) %>% 
  separate(col = word, 
           into = c("word", "pos"),
           sep = "/") -> ppp_df
readRDS("tmj_v.rds") %>% 
  tibble(text = .) %>% 
  # 품사로 토큰화
  unnest_tokens(word, text, token  = pos) %>% 
  separate(col = word, 
           into = c("word", "pos"),
           sep = "/") -> tmj_df
```

## 빈도 분석

### 총 단어

셀 하나에 토큰이 하나씩 저장돼 있으므로 데이터프레임의 행 갯수가 강령에 사용된 단어의 수.


```r
nrow(ppp_df) -> n_ppp
nrow(tmj_df) -> n_tmj
data.table(
  국민의당 = n_ppp,
  더불어민주당 = n_tmj
) %>% gt() %>% 
  tab_header("강령 어휘 수")
```

```{=html}
<div id="tvchuebrxv" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#tvchuebrxv .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#tvchuebrxv .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#tvchuebrxv .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#tvchuebrxv .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#tvchuebrxv .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tvchuebrxv .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#tvchuebrxv .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#tvchuebrxv .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#tvchuebrxv .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#tvchuebrxv .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#tvchuebrxv .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#tvchuebrxv .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#tvchuebrxv .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#tvchuebrxv .gt_from_md > :first-child {
  margin-top: 0;
}

#tvchuebrxv .gt_from_md > :last-child {
  margin-bottom: 0;
}

#tvchuebrxv .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#tvchuebrxv .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#tvchuebrxv .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#tvchuebrxv .gt_row_group_first td {
  border-top-width: 2px;
}

#tvchuebrxv .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#tvchuebrxv .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#tvchuebrxv .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#tvchuebrxv .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tvchuebrxv .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#tvchuebrxv .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#tvchuebrxv .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#tvchuebrxv .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tvchuebrxv .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#tvchuebrxv .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#tvchuebrxv .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#tvchuebrxv .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#tvchuebrxv .gt_left {
  text-align: left;
}

#tvchuebrxv .gt_center {
  text-align: center;
}

#tvchuebrxv .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#tvchuebrxv .gt_font_normal {
  font-weight: normal;
}

#tvchuebrxv .gt_font_bold {
  font-weight: bold;
}

#tvchuebrxv .gt_font_italic {
  font-style: italic;
}

#tvchuebrxv .gt_super {
  font-size: 65%;
}

#tvchuebrxv .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#tvchuebrxv .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#tvchuebrxv .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#tvchuebrxv .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#tvchuebrxv .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#tvchuebrxv .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="2" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>강령 어휘 수</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">국민의당</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">더불어민주당</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_right">6339</td>
<td class="gt_row gt_right">11244</td></tr>
  </tbody>
  
  
</table>
</div>
```


### 상위 빈도 명사

정당별 총 사용 어휘로 나눈 상대빈도 계산. 사용 단어 수가 6천 ~1만이므로, 10000분율로 계산. 


```r
# 국민의당 강령 사용 어휘수
ppp_df -> df
n_ppp -> n_total
# 명사 빈도
df %>% 
  # 단어 길이 1개는 분석에서 제외
  filter(str_length(word) > 1) %>% 
  # 명사만 선택
  filter(pos == 'nng') %>% 
  # 단어 빈도 계산해 정렬
  count(word, sort = T) %>% 
  # 1만분률 계산
  mutate(n_bytotal10000 = round(n/n_total * 10000, 0)) %>% 
  head(15) -> top_ppp

# 더불어민주당 강령 사용 어휘수
tmj_df -> df
n_tmj -> n_total
# 명사 빈도
df %>% 
  filter(str_length(word) > 1) %>% 
  filter(pos == 'nng') %>% 
  count(word, sort = T) %>% 
  mutate(n_bytotal10000 = round(n/n_total * 10000, 0)) %>% 
  head(15) -> top_tmj

# 표 하나로 결합
bind_cols(top_ppp, top_tmj) %>% 
  gt() %>% tab_header(
    "상위 빈도 명사"
  ) %>% tab_spanner(
    label = "국민의당",
    columns = 1:3
  ) %>% tab_spanner(
    label = "더불어민주당",
    columns = 4:6
  ) %>% cols_label(
    word...1 = "명사",
    n...2 = "빈도",
    n_bytotal10000...3 = "만분율",
    word...4 = "명사",
    n...5 = "빈도",
    n_bytotal10000...6 = "만분율"
  )
```

```
## New names:
## • `word` -> `word...1`
## • `n` -> `n...2`
## • `n_bytotal10000` -> `n_bytotal10000...3`
## • `word` -> `word...4`
## • `n` -> `n...5`
## • `n_bytotal10000` -> `n_bytotal10000...6`
```

```{=html}
<div id="xhcmpzriqg" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#xhcmpzriqg .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#xhcmpzriqg .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#xhcmpzriqg .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#xhcmpzriqg .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#xhcmpzriqg .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xhcmpzriqg .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#xhcmpzriqg .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#xhcmpzriqg .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#xhcmpzriqg .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#xhcmpzriqg .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#xhcmpzriqg .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#xhcmpzriqg .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#xhcmpzriqg .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#xhcmpzriqg .gt_from_md > :first-child {
  margin-top: 0;
}

#xhcmpzriqg .gt_from_md > :last-child {
  margin-bottom: 0;
}

#xhcmpzriqg .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#xhcmpzriqg .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#xhcmpzriqg .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#xhcmpzriqg .gt_row_group_first td {
  border-top-width: 2px;
}

#xhcmpzriqg .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#xhcmpzriqg .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#xhcmpzriqg .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#xhcmpzriqg .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xhcmpzriqg .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#xhcmpzriqg .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#xhcmpzriqg .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#xhcmpzriqg .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#xhcmpzriqg .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#xhcmpzriqg .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#xhcmpzriqg .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#xhcmpzriqg .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#xhcmpzriqg .gt_left {
  text-align: left;
}

#xhcmpzriqg .gt_center {
  text-align: center;
}

#xhcmpzriqg .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#xhcmpzriqg .gt_font_normal {
  font-weight: normal;
}

#xhcmpzriqg .gt_font_bold {
  font-weight: bold;
}

#xhcmpzriqg .gt_font_italic {
  font-style: italic;
}

#xhcmpzriqg .gt_super {
  font-size: 65%;
}

#xhcmpzriqg .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#xhcmpzriqg .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#xhcmpzriqg .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#xhcmpzriqg .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#xhcmpzriqg .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#xhcmpzriqg .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="6" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>상위 빈도 명사</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">국민의당</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">더불어민주당</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">58</td>
<td class="gt_row gt_right">91</td>
<td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">132</td>
<td class="gt_row gt_right">117</td></tr>
    <tr><td class="gt_row gt_left">국민</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">63</td>
<td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">109</td>
<td class="gt_row gt_right">97</td></tr>
    <tr><td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">39</td>
<td class="gt_row gt_right">62</td>
<td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">84</td>
<td class="gt_row gt_right">75</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">36</td>
<td class="gt_row gt_right">57</td>
<td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">69</td>
<td class="gt_row gt_right">61</td></tr>
    <tr><td class="gt_row gt_left">마련</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_right">43</td>
<td class="gt_row gt_left">교육</td>
<td class="gt_row gt_right">63</td>
<td class="gt_row gt_right">56</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_right">41</td>
<td class="gt_row gt_left">국민</td>
<td class="gt_row gt_right">59</td>
<td class="gt_row gt_right">52</td></tr>
    <tr><td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">22</td>
<td class="gt_row gt_right">35</td>
<td class="gt_row gt_left">지원</td>
<td class="gt_row gt_right">57</td>
<td class="gt_row gt_right">51</td></tr>
    <tr><td class="gt_row gt_left">변화</td>
<td class="gt_row gt_right">21</td>
<td class="gt_row gt_right">33</td>
<td class="gt_row gt_left">지역</td>
<td class="gt_row gt_right">56</td>
<td class="gt_row gt_right">50</td></tr>
    <tr><td class="gt_row gt_left">정치</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">32</td>
<td class="gt_row gt_left">국가</td>
<td class="gt_row gt_right">49</td>
<td class="gt_row gt_right">44</td></tr>
    <tr><td class="gt_row gt_left">환경</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">32</td>
<td class="gt_row gt_left">구축</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_left">문화</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">적극</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_left">실현</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">교육</td>
<td class="gt_row gt_right">17</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_left">기반</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">36</td></tr>
    <tr><td class="gt_row gt_left">정책</td>
<td class="gt_row gt_right">17</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_left">협력</td>
<td class="gt_row gt_right">39</td>
<td class="gt_row gt_right">35</td></tr>
    <tr><td class="gt_row gt_left">권력</td>
<td class="gt_row gt_right">16</td>
<td class="gt_row gt_right">25</td>
<td class="gt_row gt_left">확대</td>
<td class="gt_row gt_right">38</td>
<td class="gt_row gt_right">34</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 함께 사용한 단어

`dplyr`패키지의 `inner_join()`함수로 두 정당 자료를 결합해 함께 사용한 단어를 추출한다. 


```r
# 데이터프레임 공통어 결합
inner_join(
  ppp_df %>% count(word, sort = T),
  tmj_df %>% count(word, sort = T),
  by = c("word")
  ) %>% filter(str_length(word) > 1) %>% 
  # 1만분률 계산
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000) %>% 
  arrange(desc(ppp_by10000)) %>% 
  head(15) %>% 
  gt() %>% tab_header(
    "양당이 함께 사용한 단어"
  ) %>% tab_spanner(
    label = "빈도",
    columns = 2:3
  ) %>% tab_spanner(
    label = "만분률",
    columns = 4:5
  ) %>% cols_label(
    word = "단어",
    n.x = "국민의당",
    ppp_by10000 = "국민의당",
    n.y = "더불어민주당",
    tmj_by10000 = "더불어민주당"
  )
```

```{=html}
<div id="azggmpeecm" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#azggmpeecm .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#azggmpeecm .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#azggmpeecm .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#azggmpeecm .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#azggmpeecm .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#azggmpeecm .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#azggmpeecm .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#azggmpeecm .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#azggmpeecm .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#azggmpeecm .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#azggmpeecm .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#azggmpeecm .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#azggmpeecm .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#azggmpeecm .gt_from_md > :first-child {
  margin-top: 0;
}

#azggmpeecm .gt_from_md > :last-child {
  margin-bottom: 0;
}

#azggmpeecm .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#azggmpeecm .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#azggmpeecm .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#azggmpeecm .gt_row_group_first td {
  border-top-width: 2px;
}

#azggmpeecm .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#azggmpeecm .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#azggmpeecm .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#azggmpeecm .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#azggmpeecm .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#azggmpeecm .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#azggmpeecm .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#azggmpeecm .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#azggmpeecm .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#azggmpeecm .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#azggmpeecm .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#azggmpeecm .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#azggmpeecm .gt_left {
  text-align: left;
}

#azggmpeecm .gt_center {
  text-align: center;
}

#azggmpeecm .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#azggmpeecm .gt_font_normal {
  font-weight: normal;
}

#azggmpeecm .gt_font_bold {
  font-weight: bold;
}

#azggmpeecm .gt_font_italic {
  font-style: italic;
}

#azggmpeecm .gt_super {
  font-size: 65%;
}

#azggmpeecm .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#azggmpeecm .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#azggmpeecm .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#azggmpeecm .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#azggmpeecm .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#azggmpeecm .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="5" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>양당이 함께 사용한 단어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1">단어</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">빈도</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">만분률</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">국민의당</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">더불어민주당</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">국민의당</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">더불어민주당</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">한다</td>
<td class="gt_row gt_right">124</td>
<td class="gt_row gt_right">301</td>
<td class="gt_row gt_right">195.6</td>
<td class="gt_row gt_right">267.7</td></tr>
    <tr><td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">58</td>
<td class="gt_row gt_right">132</td>
<td class="gt_row gt_right">91.5</td>
<td class="gt_row gt_right">117.4</td></tr>
    <tr><td class="gt_row gt_left">도록</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">34</td>
<td class="gt_row gt_right">75.7</td>
<td class="gt_row gt_right">30.2</td></tr>
    <tr><td class="gt_row gt_left">으로</td>
<td class="gt_row gt_right">45</td>
<td class="gt_row gt_right">74</td>
<td class="gt_row gt_right">71.0</td>
<td class="gt_row gt_right">65.8</td></tr>
    <tr><td class="gt_row gt_left">국민</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">59</td>
<td class="gt_row gt_right">63.1</td>
<td class="gt_row gt_right">52.5</td></tr>
    <tr><td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">39</td>
<td class="gt_row gt_right">69</td>
<td class="gt_row gt_right">61.5</td>
<td class="gt_row gt_right">61.4</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">36</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_right">56.8</td>
<td class="gt_row gt_right">24.9</td></tr>
    <tr><td class="gt_row gt_left">위해</td>
<td class="gt_row gt_right">33</td>
<td class="gt_row gt_right">62</td>
<td class="gt_row gt_right">52.1</td>
<td class="gt_row gt_right">55.1</td></tr>
    <tr><td class="gt_row gt_left">우리</td>
<td class="gt_row gt_right">30</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">47.3</td>
<td class="gt_row gt_right">8.9</td></tr>
    <tr><td class="gt_row gt_left">마련</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">42.6</td>
<td class="gt_row gt_right">17.8</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">9.8</td></tr>
    <tr><td class="gt_row gt_left">위한</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_right">31</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">27.6</td></tr>
    <tr><td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">22</td>
<td class="gt_row gt_right">113</td>
<td class="gt_row gt_right">34.7</td>
<td class="gt_row gt_right">100.5</td></tr>
    <tr><td class="gt_row gt_left">변화</td>
<td class="gt_row gt_right">21</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">33.1</td>
<td class="gt_row gt_right">8.0</td></tr>
    <tr><td class="gt_row gt_left">정치</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">31.6</td>
<td class="gt_row gt_right">17.8</td></tr>
  </tbody>
  
  
</table>
</div>
```





### 상대빈도

국민의당과 더불어민주당 문서 단어의 상대적인 빈도 계산. 

#### 상위공통어 중 상대적으로 더 많이 쓴 단어 

양당이 함께 많이 사용한 단어 중 각각 국민의당과 더불어민주당 기준으로 정렬해 비교한다. 


```r
# 국민의당 기준 공통어 데이터프레임 결합
inner_join(
  ppp_df %>% count(word, sort = T),
  tmj_df %>% count(word, sort = T),
  by = c("word")
  ) %>% filter(str_length(word) > 1) %>% 
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000,
  # 사용 어휘 차이 빈도 계산
         diff = ppp_by10000 - tmj_by10000) %>%
  # 차이가 큰 순서를 국민의당기준으로 정렬
  arrange(desc(diff)) %>% 
  head(15) -> com_ppp
# 더불어민주당 기준 공통어 데이터프레임 결합
inner_join(
  ppp_df %>% count(word, sort = T),
  tmj_df %>% count(word, sort = T),
  by = c("word")
  ) %>% filter(str_length(word) > 1) %>% 
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000,
  # 사용 어휘 차이 빈도 계산       
         diff = ppp_by10000 - tmj_by10000) %>% 
  # 차이가 큰 순서를 더불어민주당 순서로 정렬
  arrange(diff) %>% 
  head(15) -> com_tmj

# 데이터프레임 결합
bind_cols(
  com_ppp %>% select.(-c(n.x, n.y)), 
  com_tmj %>% select.(-c(n.x, n.y)) 
) %>% gt() %>% tab_header(
  "공동어 중 상대적으로 더 많이 쓴 단어"
  ) %>% tab_spanner(
    label = "국민의당 기준",
    columns = 1:4
  ) %>% tab_spanner(
    label = "더불어민주당 기준",
    columns = 5:8
  ) %>% cols_label(
    word...1 = "명사",
    ppp_by10000...2 = "만분율ppp",
    tmj_by10000...3	 = "만분율tmj",
    diff...4 = "차이",
    word...5 = "명사",
    ppp_by10000...6 = "만분율ppp",
    tmj_by10000...7	 = "만분율tmj",
    diff...8 = "차이",
  )
```

```
## New names:
## • `word` -> `word...1`
## • `ppp_by10000` -> `ppp_by10000...2`
## • `tmj_by10000` -> `tmj_by10000...3`
## • `diff` -> `diff...4`
## • `word` -> `word...5`
## • `ppp_by10000` -> `ppp_by10000...6`
## • `tmj_by10000` -> `tmj_by10000...7`
## • `diff` -> `diff...8`
```

```{=html}
<div id="obhrjhgzfj" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#obhrjhgzfj .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#obhrjhgzfj .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#obhrjhgzfj .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#obhrjhgzfj .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#obhrjhgzfj .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#obhrjhgzfj .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#obhrjhgzfj .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#obhrjhgzfj .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#obhrjhgzfj .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#obhrjhgzfj .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#obhrjhgzfj .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#obhrjhgzfj .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#obhrjhgzfj .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#obhrjhgzfj .gt_from_md > :first-child {
  margin-top: 0;
}

#obhrjhgzfj .gt_from_md > :last-child {
  margin-bottom: 0;
}

#obhrjhgzfj .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#obhrjhgzfj .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#obhrjhgzfj .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#obhrjhgzfj .gt_row_group_first td {
  border-top-width: 2px;
}

#obhrjhgzfj .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#obhrjhgzfj .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#obhrjhgzfj .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#obhrjhgzfj .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#obhrjhgzfj .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#obhrjhgzfj .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#obhrjhgzfj .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#obhrjhgzfj .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#obhrjhgzfj .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#obhrjhgzfj .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#obhrjhgzfj .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#obhrjhgzfj .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#obhrjhgzfj .gt_left {
  text-align: left;
}

#obhrjhgzfj .gt_center {
  text-align: center;
}

#obhrjhgzfj .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#obhrjhgzfj .gt_font_normal {
  font-weight: normal;
}

#obhrjhgzfj .gt_font_bold {
  font-weight: bold;
}

#obhrjhgzfj .gt_font_italic {
  font-style: italic;
}

#obhrjhgzfj .gt_super {
  font-size: 65%;
}

#obhrjhgzfj .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#obhrjhgzfj .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#obhrjhgzfj .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#obhrjhgzfj .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#obhrjhgzfj .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#obhrjhgzfj .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="8" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>공동어 중 상대적으로 더 많이 쓴 단어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="4">
        <span class="gt_column_spanner">국민의당 기준</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="4">
        <span class="gt_column_spanner">더불어민주당 기준</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율tmj</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">차이</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">만분율tmj</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">차이</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">도록</td>
<td class="gt_row gt_right">75.7</td>
<td class="gt_row gt_right">30.2</td>
<td class="gt_row gt_right">45.5</td>
<td class="gt_row gt_left">한다</td>
<td class="gt_row gt_right">195.6</td>
<td class="gt_row gt_right">267.7</td>
<td class="gt_row gt_right">-72.1</td></tr>
    <tr><td class="gt_row gt_left">우리</td>
<td class="gt_row gt_right">47.3</td>
<td class="gt_row gt_right">8.9</td>
<td class="gt_row gt_right">38.4</td>
<td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">34.7</td>
<td class="gt_row gt_right">100.5</td>
<td class="gt_row gt_right">-65.8</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">56.8</td>
<td class="gt_row gt_right">24.9</td>
<td class="gt_row gt_right">31.9</td>
<td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">28.4</td>
<td class="gt_row gt_right">74.7</td>
<td class="gt_row gt_right">-46.3</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">9.8</td>
<td class="gt_row gt_right">31.2</td>
<td class="gt_row gt_left">지원</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">50.7</td>
<td class="gt_row gt_right">-31.8</td></tr>
    <tr><td class="gt_row gt_left">변화</td>
<td class="gt_row gt_right">33.1</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">25.1</td>
<td class="gt_row gt_left">문화</td>
<td class="gt_row gt_right">11.0</td>
<td class="gt_row gt_right">42.7</td>
<td class="gt_row gt_right">-31.7</td></tr>
    <tr><td class="gt_row gt_left">마련</td>
<td class="gt_row gt_right">42.6</td>
<td class="gt_row gt_right">17.8</td>
<td class="gt_row gt_right">24.8</td>
<td class="gt_row gt_left">실현</td>
<td class="gt_row gt_right">12.6</td>
<td class="gt_row gt_right">42.7</td>
<td class="gt_row gt_right">-30.1</td></tr>
    <tr><td class="gt_row gt_left">는다</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">1.8</td>
<td class="gt_row gt_right">18.7</td>
<td class="gt_row gt_left">지역</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">49.8</td>
<td class="gt_row gt_right">-29.3</td></tr>
    <tr><td class="gt_row gt_left">권력</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">7.1</td>
<td class="gt_row gt_right">18.1</td>
<td class="gt_row gt_left">교육</td>
<td class="gt_row gt_right">26.8</td>
<td class="gt_row gt_right">56.0</td>
<td class="gt_row gt_right">-29.2</td></tr>
    <tr><td class="gt_row gt_left">모두</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">17.2</td>
<td class="gt_row gt_left">협력</td>
<td class="gt_row gt_right">6.3</td>
<td class="gt_row gt_right">34.7</td>
<td class="gt_row gt_right">-28.4</td></tr>
    <tr><td class="gt_row gt_left">개혁</td>
<td class="gt_row gt_right">22.1</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">14.1</td>
<td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">91.5</td>
<td class="gt_row gt_right">117.4</td>
<td class="gt_row gt_right">-25.9</td></tr>
    <tr><td class="gt_row gt_left">정치</td>
<td class="gt_row gt_right">31.6</td>
<td class="gt_row gt_right">17.8</td>
<td class="gt_row gt_right">13.8</td>
<td class="gt_row gt_left">구축</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">42.7</td>
<td class="gt_row gt_right">-23.8</td></tr>
    <tr><td class="gt_row gt_left">세대</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">5.3</td>
<td class="gt_row gt_right">13.6</td>
<td class="gt_row gt_left">국가</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">43.6</td>
<td class="gt_row gt_right">-23.1</td></tr>
    <tr><td class="gt_row gt_left">위한</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">27.6</td>
<td class="gt_row gt_right">13.4</td>
<td class="gt_row gt_left">에너지</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">24.0</td>
<td class="gt_row gt_right">-22.4</td></tr>
    <tr><td class="gt_row gt_left">기회</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">12.5</td>
<td class="gt_row gt_right">12.7</td>
<td class="gt_row gt_left">참여</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">24.9</td>
<td class="gt_row gt_right">-21.7</td></tr>
    <tr><td class="gt_row gt_left">10</td>
<td class="gt_row gt_right">14.2</td>
<td class="gt_row gt_right">2.7</td>
<td class="gt_row gt_right">11.5</td>
<td class="gt_row gt_left">체계</td>
<td class="gt_row gt_right">6.3</td>
<td class="gt_row gt_right">27.6</td>
<td class="gt_row gt_right">-21.3</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 문서 전반의 상대빈도

문서 전체의 사용 단어의 빈도를 계산해 두 정당에서 상대적으로 더 많이 사용한 단어가 무엇인지 계산. 

`tidylo`패키지의 `bind_log_odds()`함수로 계산하는 가중로그승산비를 이용.

- bind_log_odds(tbl, set, feature, n, uninformative = FALSE, unweighted = FALSE)
 -tbl: 정돈데이터(feature와 set이 하나의 행에 저장)
 -set: feature를 비교하기 위한 set(group)에 대한 정보(예: 긍정 vs. 부정)이 저장된 열
 - feature: feature(단어나 바이그램 등의 텍스트자료)가 저장된 열.
 - n: feature-set의 빈도를 저장한 열
 - uninformative: uninformative 디리슐레 분포 사용 여부. 기본값은 FALSE
 - unweighted: 비가중 로그승산 사용여부. 기본값은 FALSE. TRUE로 지정하면 비가중 로그승산비(log_odds) 열을 추가
 
가중로그승산비에 대한 보다 자세한 설명은 [R텍스트마이닝 8.2.4 가중로그승산비 참조](https://r2bit.com/book_tm/tf-idf.html#%EA%B0%80%EC%A4%91%EB%A1%9C%EA%B7%B8%EC%8A%B9%EC%82%B0%EB%B9%84)


```r
# 행방향 결합. 1 = 국민의당 2 = 더불어민주당
bind_rows(ppp_df, tmj_df, .id = "party")  %>% 
  filter(str_length(word) > 1) %>% 
  count(word, party) %>% 
  bind_log_odds(set = party,
                feature = word, 
                n = n) %>% 
  arrange(-log_odds_weighted) -> weighted_log_odds_df

# 열 결합
bind_cols(
  #국민의당 상대적으로 더 많이 사용한 단어
  weighted_log_odds_df %>%   
  group_by(party = ifelse(party == 1, "ppp", "tmj")) %>% 
  arrange(party) %>% 
  select.(-party) %>%   
  head(15),
  # 더불어민주당이 상대적으로 더 많이 사용한 단어
  weighted_log_odds_df %>%   
  group_by(party = ifelse(party == 1, "ppp", "tmj")) %>% 
  arrange(desc(party)) %>% 
  select.(-party) %>%     
  head(15) 
  ) %>% gt() %>% tab_header(
  "상대적으로 많이 사용한 단어"
  ) %>% tab_spanner(
    label = "국민의당 기준",
    columns = 1:3
  ) %>% tab_spanner(
    label = "더불어민주당 기준",
    columns = 4:6
  ) %>% cols_label(
    word...1 = "명사",
    n...2 = "빈도",
    log_odds_weighted...3 = "가중상대빈도",
    word...4 = "명사",
    n...5 = "빈도",
    log_odds_weighted...6 = "가중상대빈도"
  ) %>% fmt_number(
    columns = starts_with("log"), 
    decimals = 2
  )
```

```
## New names:
## • `word` -> `word...1`
## • `n` -> `n...2`
## • `log_odds_weighted` -> `log_odds_weighted...3`
## • `word` -> `word...4`
## • `n` -> `n...5`
## • `log_odds_weighted` -> `log_odds_weighted...6`
```

```{=html}
<div id="gismrmlfjy" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#gismrmlfjy .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#gismrmlfjy .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#gismrmlfjy .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#gismrmlfjy .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#gismrmlfjy .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#gismrmlfjy .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#gismrmlfjy .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#gismrmlfjy .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#gismrmlfjy .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#gismrmlfjy .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#gismrmlfjy .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#gismrmlfjy .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#gismrmlfjy .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#gismrmlfjy .gt_from_md > :first-child {
  margin-top: 0;
}

#gismrmlfjy .gt_from_md > :last-child {
  margin-bottom: 0;
}

#gismrmlfjy .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#gismrmlfjy .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#gismrmlfjy .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#gismrmlfjy .gt_row_group_first td {
  border-top-width: 2px;
}

#gismrmlfjy .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#gismrmlfjy .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#gismrmlfjy .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#gismrmlfjy .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#gismrmlfjy .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#gismrmlfjy .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#gismrmlfjy .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#gismrmlfjy .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#gismrmlfjy .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#gismrmlfjy .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#gismrmlfjy .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#gismrmlfjy .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#gismrmlfjy .gt_left {
  text-align: left;
}

#gismrmlfjy .gt_center {
  text-align: center;
}

#gismrmlfjy .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#gismrmlfjy .gt_font_normal {
  font-weight: normal;
}

#gismrmlfjy .gt_font_bold {
  font-weight: bold;
}

#gismrmlfjy .gt_font_italic {
  font-style: italic;
}

#gismrmlfjy .gt_super {
  font-size: 65%;
}

#gismrmlfjy .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#gismrmlfjy .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#gismrmlfjy .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#gismrmlfjy .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#gismrmlfjy .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#gismrmlfjy .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="6" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>상대적으로 많이 사용한 단어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">국민의당 기준</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">더불어민주당 기준</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">가중상대빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">명사</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">가중상대빈도</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">사법</td>
<td class="gt_row gt_right">7</td>
<td class="gt_row gt_right">2.20</td>
<td class="gt_row gt_left">미디어</td>
<td class="gt_row gt_right">19</td>
<td class="gt_row gt_right">2.50</td></tr>
    <tr><td class="gt_row gt_left">앞장서</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_right">2.04</td>
<td class="gt_row gt_left">생활</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">2.43</td></tr>
    <tr><td class="gt_row gt_left">우리</td>
<td class="gt_row gt_right">30</td>
<td class="gt_row gt_right">1.95</td>
<td class="gt_row gt_left">예술</td>
<td class="gt_row gt_right">16</td>
<td class="gt_row gt_right">2.29</td></tr>
    <tr><td class="gt_row gt_left">폐지</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">1.86</td>
<td class="gt_row gt_left">상생</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">도록</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">1.79</td>
<td class="gt_row gt_left">으로써</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_right">1.68</td>
<td class="gt_row gt_left">포용</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">경우</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">당원</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">내일</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">여성</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">대통령</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">의료</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">동물</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">촉진</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">1.72</td></tr>
    <tr><td class="gt_row gt_left">비리</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">공동</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">스스로</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">소통</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">앞장선다</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">임금</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">인간</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">탄소</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">최대한</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">책무</td>
<td class="gt_row gt_right">7</td>
<td class="gt_row gt_right">1.52</td></tr>
  </tbody>
  
  
</table>
</div>
```



### 감정어 빈도

감정어가 포함돼 데이터프레임 사전과 분석대상 문서를 `inner_join()`한다. 

#### 감정사전

먼저 간정사전 만들기. 

- [KNU한국어감성사전](https://github.com/park1200656/KnuSentiLex)
군산대 소프트웨어융합공학과 Data Intelligence Lab에서 개발한 한국어감정사전이다. 표준국어대사전을 구성하는 각 단어의 뜻풀이를 분석하여 긍부정어를 추출했다.



```r
# 코드만 표시
url_v <- "https://github.com/park1200656/KnuSentiLex/archive/refs/heads/master.zip"
dest_v <- "knusenti.zip"
download.file(url = url_v, 
              destfile = dest_v,
              mode = "wb")
# 압축을 풀면 KnuSentiLex-master 폴더 생성
unzip("knusenti.zip")
# 생성된 폴더내 9번째 파일이 사전파일. 
# 이 파일명을 사전파일 이름 지정
senti_name_v <- list.files("KnuSentiLex-master/.")[9]
# 데이터프레임으로 이입
senti_dic_df <- read_tsv(str_c("data/KnuSentiLex-master/", senti_name_v), col_names = F)
# 데이터프레임 열 이름 변경
senti_dic_df <- senti_dic_df %>% rename(word = X1, sScore = X2)
# 감정값 오류 수정
senti_dic_df %>% 
  filter(!is.na(sScore)) %>% 
  add_row(word = "갈등", sScore = -1) -> senti_dic_df 
# 수정 확인
senti_dic_df %>% 
  filter(!is.na(sScore)) %>% count(sScore)
# 파일로 저장
saveRDS(senti_dic_df, "knu_dic.rds")
# 저장 확인
list.files(pattern = "^knu")
```


#### 단어 빈도

앞서 만든 감정사전 데이터프레임과 분석대상 문서의 데이터프레임을 `inner_join()`


```r
# 감정사전과 결합
readRDS("knu_dic.rds") -> knu_dic_df
ppp_df %>% inner_join(knu_dic_df) -> emo_ppp
```

```
## Joining, by = "word"
```

```r
tmj_df %>% inner_join(knu_dic_df) -> emo_tmj
```

```
## Joining, by = "word"
```

```r
# 두 정당의 값을 보기 편하게 열방향으로 결합
bind_cols(
  # 국민의당
  emo_ppp %>% 
    count(word, sScore, sort = T) %>% 
    filter(str_length(word) > 1) %>% 
    mutate(word = reorder(word, n)) %>% 
    head(15),
  # 더불어민주당
  emo_tmj %>% 
    count(word, sScore, sort = T) %>% 
    filter(str_length(word) > 1) %>% 
    mutate(word = reorder(word, n)) %>% 
    head(15) 
) %>% gt() %>% tab_header(
  "많이 사용한 감정어"
  ) %>% tab_spanner(
    label = "국민의당",
    columns = 1:3
  ) %>% tab_spanner(
    label = "더불어민주당",
    columns = 4:6
  ) %>% cols_label(
    word...1 = "감정어",
    sScore...2 = "감정점수",
    n...3 = "빈도",
    word...4 = "감정어",
    sScore...5 = "감정점수",
    n...6 = "빈도"
  ) 
```

```
## New names:
## • `word` -> `word...1`
## • `sScore` -> `sScore...2`
## • `n` -> `n...3`
## • `word` -> `word...4`
## • `sScore` -> `sScore...5`
## • `n` -> `n...6`
```

```{=html}
<div id="rutuvsgwjd" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#rutuvsgwjd .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#rutuvsgwjd .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#rutuvsgwjd .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#rutuvsgwjd .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#rutuvsgwjd .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rutuvsgwjd .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#rutuvsgwjd .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#rutuvsgwjd .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#rutuvsgwjd .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#rutuvsgwjd .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#rutuvsgwjd .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#rutuvsgwjd .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#rutuvsgwjd .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#rutuvsgwjd .gt_from_md > :first-child {
  margin-top: 0;
}

#rutuvsgwjd .gt_from_md > :last-child {
  margin-bottom: 0;
}

#rutuvsgwjd .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#rutuvsgwjd .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#rutuvsgwjd .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#rutuvsgwjd .gt_row_group_first td {
  border-top-width: 2px;
}

#rutuvsgwjd .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#rutuvsgwjd .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#rutuvsgwjd .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#rutuvsgwjd .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rutuvsgwjd .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#rutuvsgwjd .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#rutuvsgwjd .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#rutuvsgwjd .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#rutuvsgwjd .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#rutuvsgwjd .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#rutuvsgwjd .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#rutuvsgwjd .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#rutuvsgwjd .gt_left {
  text-align: left;
}

#rutuvsgwjd .gt_center {
  text-align: center;
}

#rutuvsgwjd .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#rutuvsgwjd .gt_font_normal {
  font-weight: normal;
}

#rutuvsgwjd .gt_font_bold {
  font-weight: bold;
}

#rutuvsgwjd .gt_font_italic {
  font-style: italic;
}

#rutuvsgwjd .gt_super {
  font-size: 65%;
}

#rutuvsgwjd .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#rutuvsgwjd .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#rutuvsgwjd .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#rutuvsgwjd .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#rutuvsgwjd .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#rutuvsgwjd .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="6" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>많이 사용한 감정어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">국민의당</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">더불어민주당</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">감정어</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">감정점수</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">감정어</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">감정점수</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_center">적극</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_center">발전</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">36</td></tr>
    <tr><td class="gt_row gt_center">혁신</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">15</td>
<td class="gt_row gt_center">혁신</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">31</td></tr>
    <tr><td class="gt_row gt_center">발전</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_center">적극</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">27</td></tr>
    <tr><td class="gt_row gt_center">함께</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_center">안정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">21</td></tr>
    <tr><td class="gt_row gt_center">행복</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_center">위기</td>
<td class="gt_row gt_right">-1</td>
<td class="gt_row gt_right">21</td></tr>
    <tr><td class="gt_row gt_center">안전</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_center">개선</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">19</td></tr>
    <tr><td class="gt_row gt_center">안정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_center">가치</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">15</td></tr>
    <tr><td class="gt_row gt_center">개선</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_center">존중</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">11</td></tr>
    <tr><td class="gt_row gt_center">존중</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_center">소득</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">10</td></tr>
    <tr><td class="gt_row gt_center">능력</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_center">안전</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">10</td></tr>
    <tr><td class="gt_row gt_center">새로운</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_center">함께</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">10</td></tr>
    <tr><td class="gt_row gt_center">조화</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_center">부담</td>
<td class="gt_row gt_right">-2</td>
<td class="gt_row gt_right">9</td></tr>
    <tr><td class="gt_row gt_center">위기</td>
<td class="gt_row gt_right">-1</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_center">새로운</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">9</td></tr>
    <tr><td class="gt_row gt_center">쾌적</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_center">조화</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">9</td></tr>
    <tr><td class="gt_row gt_center">가치</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_center">향상</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">9</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 긍정어/부정어 비율

두 정당의 강령에서 긍정어와 부정어의 비율을 계산해 비교. 


```r
emo_ppp %>% 
  mutate(감정 = case_when(
    sScore > 0 ~ "긍정",
    sScore < 0 ~ "부정",
    TRUE ~ "중립"
    )) -> emo2_ppp
emo_tmj %>% 
  mutate(감정 = case_when(
    sScore > 0 ~ "긍정",
    sScore < 0 ~ "부정",
   TRUE ~ "중립"
   )) -> emo2_tmj
# 공통결합
inner_join(by = "감정",
    emo2_ppp %>% tabyl(감정) %>% 
    adorn_totals() %>% 
    adorn_pct_formatting(),
    emo2_tmj %>% tabyl(감정) %>% 
    adorn_totals() %>% 
    adorn_pct_formatting()
) %>% gt() %>% tab_header(
  "감정어 비율"
  ) %>% tab_spanner(
    columns = 2:3,
    label = "국민의당"
  ) %>% tab_spanner(
    columns = 4:5,
    label = "더불어민주당"
  ) %>% cols_label(
    n.x = "빈도",
    percent.x = "백분율",
    n.y = "빈도",
    percent.y = "백분율"
  )  
```

```{=html}
<div id="viyrvjxcaa" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#viyrvjxcaa .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#viyrvjxcaa .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#viyrvjxcaa .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#viyrvjxcaa .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#viyrvjxcaa .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#viyrvjxcaa .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#viyrvjxcaa .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#viyrvjxcaa .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#viyrvjxcaa .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#viyrvjxcaa .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#viyrvjxcaa .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#viyrvjxcaa .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#viyrvjxcaa .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#viyrvjxcaa .gt_from_md > :first-child {
  margin-top: 0;
}

#viyrvjxcaa .gt_from_md > :last-child {
  margin-bottom: 0;
}

#viyrvjxcaa .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#viyrvjxcaa .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#viyrvjxcaa .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#viyrvjxcaa .gt_row_group_first td {
  border-top-width: 2px;
}

#viyrvjxcaa .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#viyrvjxcaa .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#viyrvjxcaa .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#viyrvjxcaa .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#viyrvjxcaa .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#viyrvjxcaa .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#viyrvjxcaa .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#viyrvjxcaa .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#viyrvjxcaa .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#viyrvjxcaa .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#viyrvjxcaa .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#viyrvjxcaa .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#viyrvjxcaa .gt_left {
  text-align: left;
}

#viyrvjxcaa .gt_center {
  text-align: center;
}

#viyrvjxcaa .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#viyrvjxcaa .gt_font_normal {
  font-weight: normal;
}

#viyrvjxcaa .gt_font_bold {
  font-weight: bold;
}

#viyrvjxcaa .gt_font_italic {
  font-style: italic;
}

#viyrvjxcaa .gt_super {
  font-size: 65%;
}

#viyrvjxcaa .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#viyrvjxcaa .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#viyrvjxcaa .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#viyrvjxcaa .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#viyrvjxcaa .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#viyrvjxcaa .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="5" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>감정어 비율</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1">감정</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">국민의당</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">더불어민주당</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">백분율</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">백분율</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">긍정</td>
<td class="gt_row gt_right">149</td>
<td class="gt_row gt_left">57.5%</td>
<td class="gt_row gt_right">272</td>
<td class="gt_row gt_left">51.1%</td></tr>
    <tr><td class="gt_row gt_left">부정</td>
<td class="gt_row gt_right">84</td>
<td class="gt_row gt_left">32.4%</td>
<td class="gt_row gt_right">155</td>
<td class="gt_row gt_left">29.1%</td></tr>
    <tr><td class="gt_row gt_left">중립</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_left">10.0%</td>
<td class="gt_row gt_right">105</td>
<td class="gt_row gt_left">19.7%</td></tr>
    <tr><td class="gt_row gt_left">Total</td>
<td class="gt_row gt_right">259</td>
<td class="gt_row gt_left">100.0%</td>
<td class="gt_row gt_right">532</td>
<td class="gt_row gt_left">100.0%</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 함께 사용된 긍정어


```r
inner_join(
  emo2_ppp %>% count(word, 감정, sort = T),
  emo2_tmj %>% count(word, 감정, sort = T),
  by = c("word", "감정")
) %>% 
  filter(str_length(word) > 1) %>% 
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000) %>% 
  # 감정기준 정렬
  arrange(감정) %>% 
  select.(-감정) %>% 
  head(15) %>% 
  gt() %>% tab_header(
    "양당이 함께 사용한 긍정어"
  ) %>% tab_spanner(
    columns = starts_with("n"),
    label = "빈도"
  ) %>% tab_spanner(
    columns = ends_with("10000"),
    label = "만분률"
  ) %>% cols_label(
    n.x = "ppp",
    n.y = "tmj",
    ppp_by10000 = "ppp",
    tmj_by10000 = "tmj"
  )
```

```{=html}
<div id="wojyerrsmj" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#wojyerrsmj .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#wojyerrsmj .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#wojyerrsmj .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#wojyerrsmj .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#wojyerrsmj .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wojyerrsmj .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#wojyerrsmj .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#wojyerrsmj .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#wojyerrsmj .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#wojyerrsmj .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#wojyerrsmj .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#wojyerrsmj .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#wojyerrsmj .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#wojyerrsmj .gt_from_md > :first-child {
  margin-top: 0;
}

#wojyerrsmj .gt_from_md > :last-child {
  margin-bottom: 0;
}

#wojyerrsmj .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#wojyerrsmj .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#wojyerrsmj .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#wojyerrsmj .gt_row_group_first td {
  border-top-width: 2px;
}

#wojyerrsmj .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wojyerrsmj .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#wojyerrsmj .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#wojyerrsmj .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wojyerrsmj .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wojyerrsmj .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#wojyerrsmj .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#wojyerrsmj .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wojyerrsmj .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#wojyerrsmj .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wojyerrsmj .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#wojyerrsmj .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wojyerrsmj .gt_left {
  text-align: left;
}

#wojyerrsmj .gt_center {
  text-align: center;
}

#wojyerrsmj .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#wojyerrsmj .gt_font_normal {
  font-weight: normal;
}

#wojyerrsmj .gt_font_bold {
  font-weight: bold;
}

#wojyerrsmj .gt_font_italic {
  font-style: italic;
}

#wojyerrsmj .gt_super {
  font-size: 65%;
}

#wojyerrsmj .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#wojyerrsmj .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#wojyerrsmj .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#wojyerrsmj .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#wojyerrsmj .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#wojyerrsmj .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="5" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>양당이 함께 사용한 긍정어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1">word</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">빈도</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">만분률</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">tmj</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">tmj</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">적극</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_right">28.4</td>
<td class="gt_row gt_right">24.0</td></tr>
    <tr><td class="gt_row gt_left">혁신</td>
<td class="gt_row gt_right">15</td>
<td class="gt_row gt_right">31</td>
<td class="gt_row gt_right">23.7</td>
<td class="gt_row gt_right">27.6</td></tr>
    <tr><td class="gt_row gt_left">발전</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">36</td>
<td class="gt_row gt_right">17.4</td>
<td class="gt_row gt_right">32.0</td></tr>
    <tr><td class="gt_row gt_left">함께</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">17.4</td>
<td class="gt_row gt_right">8.9</td></tr>
    <tr><td class="gt_row gt_left">행복</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">15.8</td>
<td class="gt_row gt_right">7.1</td></tr>
    <tr><td class="gt_row gt_left">안전</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">14.2</td>
<td class="gt_row gt_right">8.9</td></tr>
    <tr><td class="gt_row gt_left">안정</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">21</td>
<td class="gt_row gt_right">12.6</td>
<td class="gt_row gt_right">18.7</td></tr>
    <tr><td class="gt_row gt_left">개선</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_right">19</td>
<td class="gt_row gt_right">9.5</td>
<td class="gt_row gt_right">16.9</td></tr>
    <tr><td class="gt_row gt_left">존중</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">9.5</td>
<td class="gt_row gt_right">9.8</td></tr>
    <tr><td class="gt_row gt_left">능력</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">7.9</td>
<td class="gt_row gt_right">3.6</td></tr>
    <tr><td class="gt_row gt_left">새로운</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">7.9</td>
<td class="gt_row gt_right">8.0</td></tr>
    <tr><td class="gt_row gt_left">조화</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">7.9</td>
<td class="gt_row gt_right">8.0</td></tr>
    <tr><td class="gt_row gt_left">가치</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">15</td>
<td class="gt_row gt_right">4.7</td>
<td class="gt_row gt_right">13.3</td></tr>
    <tr><td class="gt_row gt_left">예방</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">4.7</td>
<td class="gt_row gt_right">4.4</td></tr>
    <tr><td class="gt_row gt_left">특권</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">4.7</td>
<td class="gt_row gt_right">1.8</td></tr>
  </tbody>
  
  
</table>
</div>
```

#### 함께 사용된 부정어


```r
# 공통어 결합
inner_join(
  emo2_ppp %>% count(word, 감정, sort = T),
  emo2_tmj %>% count(word, 감정, sort = T),
  by = c("word", "감정")
) %>% 
  filter(str_length(word) > 1) %>% 
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000) %>% 
  # 감정기준 정렬
  arrange(desc(감정)) %>% 
  select.(-감정) %>% 
  head(14) %>% 
  gt() %>% tab_header(
    "양당이 함께 사용한 부정어"
  ) %>% tab_spanner(
    columns = starts_with("n"),
    label = "빈도"
  ) %>% tab_spanner(
    columns = ends_with("10000"),
    label = "만분률"
  ) %>% cols_label(
    n.x = "ppp",
    n.y = "tmj",
    ppp_by10000 = "ppp",
    tmj_by10000 = "tmj"
  )
```

```{=html}
<div id="cdhzkagenv" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#cdhzkagenv .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#cdhzkagenv .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#cdhzkagenv .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#cdhzkagenv .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#cdhzkagenv .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cdhzkagenv .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#cdhzkagenv .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#cdhzkagenv .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#cdhzkagenv .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#cdhzkagenv .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#cdhzkagenv .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#cdhzkagenv .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#cdhzkagenv .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#cdhzkagenv .gt_from_md > :first-child {
  margin-top: 0;
}

#cdhzkagenv .gt_from_md > :last-child {
  margin-bottom: 0;
}

#cdhzkagenv .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#cdhzkagenv .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#cdhzkagenv .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#cdhzkagenv .gt_row_group_first td {
  border-top-width: 2px;
}

#cdhzkagenv .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#cdhzkagenv .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#cdhzkagenv .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#cdhzkagenv .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cdhzkagenv .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#cdhzkagenv .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#cdhzkagenv .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#cdhzkagenv .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#cdhzkagenv .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#cdhzkagenv .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#cdhzkagenv .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#cdhzkagenv .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#cdhzkagenv .gt_left {
  text-align: left;
}

#cdhzkagenv .gt_center {
  text-align: center;
}

#cdhzkagenv .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#cdhzkagenv .gt_font_normal {
  font-weight: normal;
}

#cdhzkagenv .gt_font_bold {
  font-weight: bold;
}

#cdhzkagenv .gt_font_italic {
  font-style: italic;
}

#cdhzkagenv .gt_super {
  font-size: 65%;
}

#cdhzkagenv .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#cdhzkagenv .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#cdhzkagenv .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#cdhzkagenv .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#cdhzkagenv .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#cdhzkagenv .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="5" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>양당이 함께 사용한 부정어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1">word</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">빈도</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2">
        <span class="gt_column_spanner">만분률</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">tmj</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">ppp</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">tmj</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">위기</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">21</td>
<td class="gt_row gt_right">6.3</td>
<td class="gt_row gt_right">18.7</td></tr>
    <tr><td class="gt_row gt_left">부담</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">4.7</td>
<td class="gt_row gt_right">8.0</td></tr>
    <tr><td class="gt_row gt_left">피해</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">4.7</td>
<td class="gt_row gt_right">4.4</td></tr>
    <tr><td class="gt_row gt_left">갈등</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">5.3</td></tr>
    <tr><td class="gt_row gt_left">소외</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">0.9</td></tr>
    <tr><td class="gt_row gt_left">장애</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">7.1</td></tr>
    <tr><td class="gt_row gt_left">질병</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">0.9</td></tr>
    <tr><td class="gt_row gt_left">폭력</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">3.6</td></tr>
    <tr><td class="gt_row gt_left">훼손</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">1.8</td></tr>
    <tr><td class="gt_row gt_left">벗어나</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">0.9</td></tr>
    <tr><td class="gt_row gt_left">불신</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">0.9</td></tr>
    <tr><td class="gt_row gt_left">어려운</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">0.9</td></tr>
    <tr><td class="gt_row gt_left">의회</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">3.6</td></tr>
    <tr><td class="gt_row gt_left">재난</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">0.9</td></tr>
  </tbody>
  
  
</table>
</div>
```



#### 감정어 상대빈도


```r
# 행방향 결합. 1 = 국민의당 2 = 더불어민주당
bind_rows(emo_ppp, emo_tmj, .id = "party") %>% 
  filter(str_length(word) > 1) %>% 
  count(word, party) %>% 
  bind_log_odds(set = party,
                feature = word, 
                n = n) %>% 
  arrange(-log_odds_weighted) -> weighted_log_odds_df
# 열결합
bind_cols(
  # 국민의당
  weighted_log_odds_df %>%   
  group_by(party = ifelse(party == 1, "ppp", "tmj")) %>% 
  arrange(party) %>% 
  select.(-party) %>%   
  head(15),  
  # 더불어민주당
  weighted_log_odds_df %>%   
  group_by(party = ifelse(party == 1, "ppp", "tmj")) %>% 
  arrange(desc(party)) %>% 
  select.(-party) %>%   
  head(15) 
) %>% gt() %>% tab_header(
  "상대적으로 더 많이 사용한 감정어"
  ) %>% tab_spanner(
    label = "국민의당 기준",
    columns = 1:3
  ) %>% tab_spanner(
    label = "더불어민주당 기준",
    columns = 4:6
  ) %>% cols_label(
    word...1 = "감정어",
    n...2 = "빈도",
    log_odds_weighted...3 = "가중상대빈도",
    word...4 = "감정어",
    n...5 = "빈도",
    log_odds_weighted...6 = "가중상대빈도"
  ) %>% fmt_number(
    columns = starts_with("log"), 
    decimals = 2
  )
```

```
## New names:
## • `word` -> `word...1`
## • `n` -> `n...2`
## • `log_odds_weighted` -> `log_odds_weighted...3`
## • `word` -> `word...4`
## • `n` -> `n...5`
## • `log_odds_weighted` -> `log_odds_weighted...6`
```

```{=html}
<div id="blrlwymeae" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#blrlwymeae .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#blrlwymeae .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#blrlwymeae .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#blrlwymeae .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#blrlwymeae .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#blrlwymeae .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#blrlwymeae .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#blrlwymeae .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#blrlwymeae .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#blrlwymeae .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#blrlwymeae .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#blrlwymeae .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#blrlwymeae .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#blrlwymeae .gt_from_md > :first-child {
  margin-top: 0;
}

#blrlwymeae .gt_from_md > :last-child {
  margin-bottom: 0;
}

#blrlwymeae .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#blrlwymeae .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#blrlwymeae .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#blrlwymeae .gt_row_group_first td {
  border-top-width: 2px;
}

#blrlwymeae .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#blrlwymeae .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#blrlwymeae .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#blrlwymeae .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#blrlwymeae .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#blrlwymeae .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#blrlwymeae .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#blrlwymeae .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#blrlwymeae .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#blrlwymeae .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#blrlwymeae .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#blrlwymeae .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#blrlwymeae .gt_left {
  text-align: left;
}

#blrlwymeae .gt_center {
  text-align: center;
}

#blrlwymeae .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#blrlwymeae .gt_font_normal {
  font-weight: normal;
}

#blrlwymeae .gt_font_bold {
  font-weight: bold;
}

#blrlwymeae .gt_font_italic {
  font-style: italic;
}

#blrlwymeae .gt_super {
  font-size: 65%;
}

#blrlwymeae .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#blrlwymeae .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#blrlwymeae .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#blrlwymeae .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#blrlwymeae .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#blrlwymeae .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="6" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>상대적으로 더 많이 사용한 감정어</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">국민의당 기준</span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner">더불어민주당 기준</span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">감정어</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">가중상대빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1">감정어</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">빈도</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">가중상대빈도</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">쾌적</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.64</td>
<td class="gt_row gt_left">남용</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.18</td></tr>
    <tr><td class="gt_row gt_left">파괴</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.42</td>
<td class="gt_row gt_left">장애인</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.18</td></tr>
    <tr><td class="gt_row gt_left">획기적으로</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.42</td>
<td class="gt_row gt_left">완성</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.02</td></tr>
    <tr><td class="gt_row gt_left">믿음</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1.15</td>
<td class="gt_row gt_left">혐오</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.02</td></tr>
    <tr><td class="gt_row gt_left">범죄</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1.15</td>
<td class="gt_row gt_left">자긍심</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">0.83</td></tr>
    <tr><td class="gt_row gt_left">재능</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1.15</td>
<td class="gt_row gt_left">개성</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">가난</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">기대</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">명예</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">두려움</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">부조리</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">부정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">원동력</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">불법</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">인정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">불안</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">전문가</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">비판</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">정상</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">수익</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">존경</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">아픔</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">지나친</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.81</td>
<td class="gt_row gt_left">역경</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
  </tbody>
  
  
</table>
</div>
```



## 단어 맥락(KWIC)

- KWIC: KeyWord In Context

특정 단어가 사용된 맥락을 파악하기 위해 특정 단어가 속한 문장 혹은 전후 단어를 함께 추출

### 단어가 포함된 문장 탐색

`crayon`패키지로 특정 단어를 장식해 문장의 어느 위치에 사용됐는지 탐색

[브라우저가 ANSI코드를 인식](https://cran.r-project.org/web/packages/fansi/vignettes/sgr-in-rmd.html)


```r
old.hooks <- fansi::set_knit_hooks(knitr::knit_hooks)
```

<STYLE type='text/css' scoped>
PRE.fansi SPAN {padding-top: .25em; padding-bottom: .25em};
</STYLE>


```r
library(glue)
library(crayon)
library(fansi)
options(crayon.enabled = TRUE)
crayon_words <- function(input_text, word = " ") {
  replaced_text <- str_replace_all(input_text, word, "{red {word}}")
  for(i in 1:length(replaced_text)) {
    crayon_text <- glue::glue_col(deparse(replaced_text[[i]]))
    print(crayon_text)
  }
}
"국가적 위기 해결에 앞장서야" %>% 
  crayon_words(input_text = ., "해결")
```

<PRE class="fansi fansi-output"><CODE>## &quot;국가적 위기 <span style='color: #BB0000;'>해결</span>에 앞장서야&quot;
</CODE></PRE>


먼저 문장 단위로 토큰화.


```r
readRDS("ppp_v.rds") %>% 
  tibble(text = .) %>% 
  unnest_tokens(output = sentence, input = text, 
                token  = "regex", pattern = "\\.") -> ppp_st
readRDS("tmj_v.rds") %>% 
  tibble(text = .) %>% 
  unnest_tokens(sentence, text, 
                token  = "regex", pattern = "\\.") -> tmj_st
```

여기서는 양당이 함께 감정어로 많이 사용했지만, 더불어민주당이 특히 많이 사용한 '위기'가 어느 맥락에서 사용됐는지 탐색한다. 

국민의당이 기술한 위기


```r
ppp_st %>% 
  filter(str_detect(sentence, "위기")) %>% 
  # 공백문자 및 공백 제거
  mutate(sentence = str_remove_all(sentence, pattern = "\r|\n"),
         sentence = str_squish(sentence)) %>% 
  pull() -> ppp_txt
crayon_words(input_text = ppp_txt, "위기")
```

<PRE class="fansi fansi-output"><CODE>## &quot;지금 우리는 세계질서의 대전환과 북한의 핵무장, 지구환경 변화와 거듭되고 있는 질병과 재난, 경제의 질적 변화로 인한 불확실성과 양극화의 심화, 인구절벽 등 중대한 <span style='color: #BB0000;'>위기</span> 앞에 서 있다&quot;
## &quot;국가적 <span style='color: #BB0000;'>위기</span> 해결에 앞장서야 할 정치는 국민이 부여한 권한에 대한 책임과 역할을 다하지 못하고, 오히려 국민 분열을 조장하는 등 사회적 혼란과 함께 정치 불신을 심화 시켜 왔다&quot;
## &quot;지방 소멸 <span style='color: #BB0000;'>위기</span>에 대비하여 각 지역이 지속가능한 경쟁력을 갖추도록 하고 삶의 질을 획기적으로 향상한다&quot;
## &quot;소득․지역․계층에 따른 격차 없이 질병에 대해 적절하게 치료를 받을 수 있는 권리를 보장하고, 감염병 등 공중보건 <span style='color: #BB0000;'>위기</span>에 효과적으로 대처한다&quot;
## &quot;나라와 국민을 위해 헌신하고 희생하신 분들은 물론 그 가족과 유족에 대해서는 정부가 끝까지 책임지고 보상과 지원을 하도록 국가의 책임을 강화하는 한편, 군인과 보훈가족이 사회적으로 존경받고 귀감이 되도록 예우를 하는 분<span style='color: #BB0000;'>위기</span>를 조성해 나간다&quot;
</CODE></PRE>



더불어민주당이 기술한 위기


```r
tmj_st %>% 
  filter(str_detect(sentence, "위기")) %>% 
  # 공백문자 및 공백 제거
  mutate(sentence = str_remove_all(sentence, pattern = "\r|\n"),
         sentence = str_squish(sentence)) %>% 
  pull() -> tmj_txt
crayon_words(input_text = tmj_txt, "위기") 
```

<PRE class="fansi fansi-output"><CODE>## &quot;4차 산업혁명 시대의 디지털 전환과 기후<span style='color: #BB0000;'>위기</span>로 인한 미래 불확실성이 증대하고 있으며, 팬데믹 이후 전세계적 경제<span style='color: #BB0000;'>위기</span>는 국민의 삶을 위협하고 사회‧경제적 불안정을 가중시키고 있다&quot;
## &quot;디지털 전환, 기후<span style='color: #BB0000;'>위기</span> 대응, 탄소중립 실현 등 대전환 시대에 경제적 생산성과 사회적 지속가능성 간의 선순환을 추구하고, 포용성장을 통해 국민 개개인의 역량이 발현될 수 있는 사회적 여건을 구축한다&quot;
## &quot;금융혁신으로 산업 경쟁력과 실물지원 기능을 향상시키고, 불안정한 국제금융질서와 금융<span style='color: #BB0000;'>위기</span>에 대응하며, 금융시장의 견제와 균형을 회복할 수 있는 관리·감독체계를 마련하여 건전성과 공공성을 확보한다&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>에 대응하기 위한 에너지 전환을 지속 추진하고 합리적인 탄소중립전략을 수립한다&quot;
## &quot;농수축협의 활성화, 가격안정을 위한 수급균형과 유통구조 개선, 탄소중립 실현 및 지역순환형 생산소비 시스템 구축 등을 통해 식량자급을 달성하고 기후<span style='color: #BB0000;'>위기</span>에 대비한다&quot;
## &quot;경제<span style='color: #BB0000;'>위기</span>를 빌미로 한 무분별한 정리해고의 남용을 방지한다&quot;
## &quot;인구감소와 고령화로 인한 지방소멸의 <span style='color: #BB0000;'>위기</span>를 극복하기 위해 도시재생 및 농‧산‧어촌의 회생을 추진한다&quot;
## &quot;이를 통해 저출생·고령화, 지방소멸 <span style='color: #BB0000;'>위기</span>, 지역 양극화를 근본적으로 치유할 수 있는 기반을 강화한다&quot;
## &quot;국민 안전을 책임지는 튼튼한 안보 한반도의 지속적인 평화와 국민 안전을 지키기 위해 신속하고 효율적인 국가<span style='color: #BB0000;'>위기</span>관리체계를 구축한다&quot;
## &quot;인류의 보편적 가치인 인권, 민주주의 구현과 함께 평화, 반테러, 비핵화, 기후 및 감염병 <span style='color: #BB0000;'>위기</span> 대응 등을 실현하기 위해 국제사회와 적극 협력하고, 글로벌 선도국가로서의 위상을 확립해 나간다&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span> 대응, 보건의료협력 등 시대적 요구에 부합하는 남북협력 의제를 설정하여, 평화경제의 새로운 기회를 만들고 남북 공동번영을 도모한다&quot;
## &quot;기후·에너지·환경 지구생태계의 회복과 보전, 특히 기후 <span style='color: #BB0000;'>위기</span>에 대한 적극 대응과 탄소중립 실현을 위해 노력한다&quot;
## &quot;에너지 취약계층의 에너지 복지를 확대하고 글로벌 공급망 <span style='color: #BB0000;'>위기</span>대응을 위해 에너지 안보를 제고한다&quot;
## &quot;지속가능한 에너지 전환 달성 기후<span style='color: #BB0000;'>위기</span> 해결을 위한 핵심 수단이자 에너지 수급의 지속가능성을 높이는 에너지 전환을 지속적으로 추진한다&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span> 대응과 탄소중립사회 구현기후<span style='color: #BB0000;'>위기</span>에 적극 대응하며 탄소중립사회로의 신속한 전환을 위해 노력한다&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>로 심화되는 불평등과 에너지 빈곤문제 해결을 위해 노력한다&quot;
## &quot;미세먼지, 황사 등 동북아시아 환경문제 해결을 위해 국가 간 협력을 강화하고, 국제사회의 책임 있는 일원으로서 기후<span style='color: #BB0000;'>위기</span>에 선제적으로 대응한다&quot;
## &quot;팬데믹 시대의 복합적 <span style='color: #BB0000;'>위기</span>를 극복하고 민주시민 역량을 가진 창의적 인재를 육성하기 위해 교육대전환을 실현한다&quot;
## &quot;기후 변화와 지속적인 감염병 등 다양한 <span style='color: #BB0000;'>위기</span>로부터 초래된 교육결손을 극복하기 위한 지원을 확대한다&quot;
## &quot;지방대 <span style='color: #BB0000;'>위기</span> 대응을 위해 지자체·대학·산업체 등이 자원을 결집하여 대학의 교육을 내실화한다&quot;
</CODE></PRE>

### 정규표현식으로 탐색

정규표현식을 이용하면 필요한 부분만 추출해서 볼수 있다. 

`위기` 앞에 임의 문자 `.` 1개부터 11개 사이 `{1,11}`를 추출해 표시


```r
ppp_txt %>% 
  str_extract(".{1,11}위기") %>% 
  crayon_words(., "위기") 
```

<PRE class="fansi fansi-output"><CODE>## &quot;인구절벽 등 중대한 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;국가적 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;지방 소멸 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;감염병 등 공중보건 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;도록 예우를 하는 분<span style='color: #BB0000;'>위기</span>&quot;
</CODE></PRE>


```r
tmj_txt %>% 
  str_extract(".{1,11}위기") %>% 
  crayon_words(., "위기") 
```

<PRE class="fansi fansi-output"><CODE>## &quot; 디지털 전환과 기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;디지털 전환, 기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot; 국제금융질서와 금융<span style='color: #BB0000;'>위기</span>&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;자급을 달성하고 기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;경제<span style='color: #BB0000;'>위기</span>&quot;
## &quot;로 인한 지방소멸의 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;·고령화, 지방소멸 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;속하고 효율적인 국가<span style='color: #BB0000;'>위기</span>&quot;
## &quot;, 기후 및 감염병 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot; 보전, 특히 기후 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;하고 글로벌 공급망 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;너지 전환 달성 기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;있는 일원으로서 기후<span style='color: #BB0000;'>위기</span>&quot;
## &quot;데믹 시대의 복합적 <span style='color: #BB0000;'>위기</span>&quot;
## &quot; 감염병 등 다양한 <span style='color: #BB0000;'>위기</span>&quot;
## &quot;지방대 <span style='color: #BB0000;'>위기</span>&quot;
</CODE></PRE>

더불어민주당은 기후위기에 대한 언급이 두드러지게 많다. 

'기후' 빈도


```r
tmj_txt %>% 
  str_extract(".{1,10}위기") %>% 
  str_detect("기후") %>% 
  tabyl() %>% 
  gt() %>% 
  tab_header("더불어민주당 위기 중 기후 비중")
```

```{=html}
<div id="sujnjwsurs" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#sujnjwsurs .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#sujnjwsurs .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#sujnjwsurs .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#sujnjwsurs .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#sujnjwsurs .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#sujnjwsurs .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#sujnjwsurs .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#sujnjwsurs .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#sujnjwsurs .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#sujnjwsurs .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#sujnjwsurs .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#sujnjwsurs .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#sujnjwsurs .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#sujnjwsurs .gt_from_md > :first-child {
  margin-top: 0;
}

#sujnjwsurs .gt_from_md > :last-child {
  margin-bottom: 0;
}

#sujnjwsurs .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#sujnjwsurs .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#sujnjwsurs .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#sujnjwsurs .gt_row_group_first td {
  border-top-width: 2px;
}

#sujnjwsurs .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#sujnjwsurs .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#sujnjwsurs .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#sujnjwsurs .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#sujnjwsurs .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#sujnjwsurs .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#sujnjwsurs .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#sujnjwsurs .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#sujnjwsurs .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#sujnjwsurs .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#sujnjwsurs .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#sujnjwsurs .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#sujnjwsurs .gt_left {
  text-align: left;
}

#sujnjwsurs .gt_center {
  text-align: center;
}

#sujnjwsurs .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#sujnjwsurs .gt_font_normal {
  font-weight: normal;
}

#sujnjwsurs .gt_font_bold {
  font-weight: bold;
}

#sujnjwsurs .gt_font_italic {
  font-style: italic;
}

#sujnjwsurs .gt_super {
  font-size: 65%;
}

#sujnjwsurs .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#sujnjwsurs .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#sujnjwsurs .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#sujnjwsurs .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#sujnjwsurs .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#sujnjwsurs .gt_fraction_denominator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: -0.05em;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="3" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>더불어민주당 위기 중 기후 비중</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">.</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">n</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">percent</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_center">FALSE</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">0.45</td></tr>
    <tr><td class="gt_row gt_center">TRUE</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">0.55</td></tr>
  </tbody>
  
  
</table>
</div>
```

## 마무리

데이터저널리즘의 본질은 자료의 수집과 분석. 시각화는 부차적인 요소. 빅데이터의 수집과 분석 역시 부차적. 모든 언론인이 늘 특종상을 받을 기사를 써야하는 것은 아니다. 데이터저널리즘도 마찬가지. 


