# 사례 {#case}

## 패키지


```r
c(
  "tidyverse", "tidytable", 
  "rvest", "janitor", 
  "tidytext", "RcppMeCab", "tidylo", 
  "gt"
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


## 정당 강령 비교

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

`tidytext`패키지와 `RcppMeCab`패키지로 품사로 토큰화해 데이터프레임으로 저장. 셀 하나당 토큰 하나. 


```r
readRDS("ppp_v.rds") %>% 
  tibble(text = .) %>% 
  # 품사로 토큰화
  unnest_tokens(word, text, token  = pos) %>% 
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

## 분석

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
<div id="zveekyzbzo" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#zveekyzbzo .gt_table {
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

#zveekyzbzo .gt_heading {
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

#zveekyzbzo .gt_title {
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

#zveekyzbzo .gt_subtitle {
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

#zveekyzbzo .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zveekyzbzo .gt_col_headings {
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

#zveekyzbzo .gt_col_heading {
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

#zveekyzbzo .gt_column_spanner_outer {
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

#zveekyzbzo .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#zveekyzbzo .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#zveekyzbzo .gt_column_spanner {
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

#zveekyzbzo .gt_group_heading {
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

#zveekyzbzo .gt_empty_group_heading {
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

#zveekyzbzo .gt_from_md > :first-child {
  margin-top: 0;
}

#zveekyzbzo .gt_from_md > :last-child {
  margin-bottom: 0;
}

#zveekyzbzo .gt_row {
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

#zveekyzbzo .gt_stub {
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

#zveekyzbzo .gt_stub_row_group {
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

#zveekyzbzo .gt_row_group_first td {
  border-top-width: 2px;
}

#zveekyzbzo .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zveekyzbzo .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#zveekyzbzo .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#zveekyzbzo .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zveekyzbzo .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zveekyzbzo .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#zveekyzbzo .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#zveekyzbzo .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zveekyzbzo .gt_footnotes {
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

#zveekyzbzo .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zveekyzbzo .gt_sourcenotes {
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

#zveekyzbzo .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zveekyzbzo .gt_left {
  text-align: left;
}

#zveekyzbzo .gt_center {
  text-align: center;
}

#zveekyzbzo .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#zveekyzbzo .gt_font_normal {
  font-weight: normal;
}

#zveekyzbzo .gt_font_bold {
  font-weight: bold;
}

#zveekyzbzo .gt_font_italic {
  font-style: italic;
}

#zveekyzbzo .gt_super {
  font-size: 65%;
}

#zveekyzbzo .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#zveekyzbzo .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#zveekyzbzo .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#zveekyzbzo .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#zveekyzbzo .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#zveekyzbzo .gt_fraction_denominator {
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
    <tr><td class="gt_row gt_right">6349</td>
<td class="gt_row gt_right">11267</td></tr>
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
  # 단어 길이 1개는 분석에 제외
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
<div id="psatnmwzhq" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#psatnmwzhq .gt_table {
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

#psatnmwzhq .gt_heading {
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

#psatnmwzhq .gt_title {
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

#psatnmwzhq .gt_subtitle {
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

#psatnmwzhq .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#psatnmwzhq .gt_col_headings {
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

#psatnmwzhq .gt_col_heading {
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

#psatnmwzhq .gt_column_spanner_outer {
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

#psatnmwzhq .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#psatnmwzhq .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#psatnmwzhq .gt_column_spanner {
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

#psatnmwzhq .gt_group_heading {
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

#psatnmwzhq .gt_empty_group_heading {
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

#psatnmwzhq .gt_from_md > :first-child {
  margin-top: 0;
}

#psatnmwzhq .gt_from_md > :last-child {
  margin-bottom: 0;
}

#psatnmwzhq .gt_row {
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

#psatnmwzhq .gt_stub {
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

#psatnmwzhq .gt_stub_row_group {
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

#psatnmwzhq .gt_row_group_first td {
  border-top-width: 2px;
}

#psatnmwzhq .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#psatnmwzhq .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#psatnmwzhq .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#psatnmwzhq .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#psatnmwzhq .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#psatnmwzhq .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#psatnmwzhq .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#psatnmwzhq .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#psatnmwzhq .gt_footnotes {
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

#psatnmwzhq .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#psatnmwzhq .gt_sourcenotes {
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

#psatnmwzhq .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#psatnmwzhq .gt_left {
  text-align: left;
}

#psatnmwzhq .gt_center {
  text-align: center;
}

#psatnmwzhq .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#psatnmwzhq .gt_font_normal {
  font-weight: normal;
}

#psatnmwzhq .gt_font_bold {
  font-weight: bold;
}

#psatnmwzhq .gt_font_italic {
  font-style: italic;
}

#psatnmwzhq .gt_super {
  font-size: 65%;
}

#psatnmwzhq .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#psatnmwzhq .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#psatnmwzhq .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#psatnmwzhq .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#psatnmwzhq .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#psatnmwzhq .gt_fraction_denominator {
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
    <tr><td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">63</td>
<td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">114</td>
<td class="gt_row gt_right">101</td></tr>
    <tr><td class="gt_row gt_left">국민</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">63</td>
<td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">87</td>
<td class="gt_row gt_right">77</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">36</td>
<td class="gt_row gt_right">57</td>
<td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">70</td>
<td class="gt_row gt_right">62</td></tr>
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
<td class="gt_row gt_right">60</td>
<td class="gt_row gt_right">53</td></tr>
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
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">환경</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">32</td>
<td class="gt_row gt_left">구축</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">교육</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_left">문화</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_left">실현</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">43</td></tr>
    <tr><td class="gt_row gt_left">적극</td>
<td class="gt_row gt_right">18</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_left">발전</td>
<td class="gt_row gt_right">45</td>
<td class="gt_row gt_right">40</td></tr>
    <tr><td class="gt_row gt_left">정책</td>
<td class="gt_row gt_right">17</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_left">기반</td>
<td class="gt_row gt_right">43</td>
<td class="gt_row gt_right">38</td></tr>
    <tr><td class="gt_row gt_left">권력</td>
<td class="gt_row gt_right">16</td>
<td class="gt_row gt_right">25</td>
<td class="gt_row gt_left">협력</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">36</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 함께 사용한 단어


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
<div id="alumsqmirf" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#alumsqmirf .gt_table {
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

#alumsqmirf .gt_heading {
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

#alumsqmirf .gt_title {
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

#alumsqmirf .gt_subtitle {
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

#alumsqmirf .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#alumsqmirf .gt_col_headings {
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

#alumsqmirf .gt_col_heading {
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

#alumsqmirf .gt_column_spanner_outer {
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

#alumsqmirf .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#alumsqmirf .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#alumsqmirf .gt_column_spanner {
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

#alumsqmirf .gt_group_heading {
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

#alumsqmirf .gt_empty_group_heading {
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

#alumsqmirf .gt_from_md > :first-child {
  margin-top: 0;
}

#alumsqmirf .gt_from_md > :last-child {
  margin-bottom: 0;
}

#alumsqmirf .gt_row {
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

#alumsqmirf .gt_stub {
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

#alumsqmirf .gt_stub_row_group {
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

#alumsqmirf .gt_row_group_first td {
  border-top-width: 2px;
}

#alumsqmirf .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#alumsqmirf .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#alumsqmirf .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#alumsqmirf .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#alumsqmirf .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#alumsqmirf .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#alumsqmirf .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#alumsqmirf .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#alumsqmirf .gt_footnotes {
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

#alumsqmirf .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#alumsqmirf .gt_sourcenotes {
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

#alumsqmirf .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#alumsqmirf .gt_left {
  text-align: left;
}

#alumsqmirf .gt_center {
  text-align: center;
}

#alumsqmirf .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#alumsqmirf .gt_font_normal {
  font-weight: normal;
}

#alumsqmirf .gt_font_bold {
  font-weight: bold;
}

#alumsqmirf .gt_font_italic {
  font-style: italic;
}

#alumsqmirf .gt_super {
  font-size: 65%;
}

#alumsqmirf .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#alumsqmirf .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#alumsqmirf .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#alumsqmirf .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#alumsqmirf .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#alumsqmirf .gt_fraction_denominator {
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
<td class="gt_row gt_right">195.3</td>
<td class="gt_row gt_right">267.2</td></tr>
    <tr><td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">58</td>
<td class="gt_row gt_right">132</td>
<td class="gt_row gt_right">91.4</td>
<td class="gt_row gt_right">117.2</td></tr>
    <tr><td class="gt_row gt_left">도록</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">34</td>
<td class="gt_row gt_right">75.6</td>
<td class="gt_row gt_right">30.2</td></tr>
    <tr><td class="gt_row gt_left">으로</td>
<td class="gt_row gt_right">45</td>
<td class="gt_row gt_right">74</td>
<td class="gt_row gt_right">70.9</td>
<td class="gt_row gt_right">65.7</td></tr>
    <tr><td class="gt_row gt_left">경제</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">70</td>
<td class="gt_row gt_right">63.0</td>
<td class="gt_row gt_right">62.1</td></tr>
    <tr><td class="gt_row gt_left">국민</td>
<td class="gt_row gt_right">40</td>
<td class="gt_row gt_right">60</td>
<td class="gt_row gt_right">63.0</td>
<td class="gt_row gt_right">53.3</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">36</td>
<td class="gt_row gt_right">28</td>
<td class="gt_row gt_right">56.7</td>
<td class="gt_row gt_right">24.9</td></tr>
    <tr><td class="gt_row gt_left">위해</td>
<td class="gt_row gt_right">33</td>
<td class="gt_row gt_right">62</td>
<td class="gt_row gt_right">52.0</td>
<td class="gt_row gt_right">55.0</td></tr>
    <tr><td class="gt_row gt_left">우리</td>
<td class="gt_row gt_right">30</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">47.3</td>
<td class="gt_row gt_right">8.9</td></tr>
    <tr><td class="gt_row gt_left">마련</td>
<td class="gt_row gt_right">27</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">42.5</td>
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
<td class="gt_row gt_right">27.5</td></tr>
    <tr><td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">22</td>
<td class="gt_row gt_right">114</td>
<td class="gt_row gt_right">34.7</td>
<td class="gt_row gt_right">101.2</td></tr>
    <tr><td class="gt_row gt_left">변화</td>
<td class="gt_row gt_right">21</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">33.1</td>
<td class="gt_row gt_right">8.0</td></tr>
    <tr><td class="gt_row gt_left">정치</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">20</td>
<td class="gt_row gt_right">31.5</td>
<td class="gt_row gt_right">17.8</td></tr>
  </tbody>
  
  
</table>
</div>
```





### 상대빈도

국민의당과 더불어민주당 문서 단어의 상대적인 빈도 계산. 

#### 상위공통어 중 상대적으로 더 많이 쓴 단어 


```r
# 국민의당 기준 공통어 데이터프레임 결합
inner_join(
  ppp_df %>% count(word, sort = T),
  tmj_df %>% count(word, sort = T),
  by = c("word")
  ) %>% filter(str_length(word) > 1) %>% 
  mutate(ppp_by10000 = round(n.x/n_ppp, 5) * 10000,
         tmj_by10000 = round(n.y/n_tmj, 5) * 10000,
         diff = ppp_by10000 - tmj_by10000) %>%
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
         diff = ppp_by10000 - tmj_by10000) %>% 
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
<div id="coztitlmrc" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#coztitlmrc .gt_table {
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

#coztitlmrc .gt_heading {
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

#coztitlmrc .gt_title {
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

#coztitlmrc .gt_subtitle {
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

#coztitlmrc .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#coztitlmrc .gt_col_headings {
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

#coztitlmrc .gt_col_heading {
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

#coztitlmrc .gt_column_spanner_outer {
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

#coztitlmrc .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#coztitlmrc .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#coztitlmrc .gt_column_spanner {
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

#coztitlmrc .gt_group_heading {
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

#coztitlmrc .gt_empty_group_heading {
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

#coztitlmrc .gt_from_md > :first-child {
  margin-top: 0;
}

#coztitlmrc .gt_from_md > :last-child {
  margin-bottom: 0;
}

#coztitlmrc .gt_row {
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

#coztitlmrc .gt_stub {
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

#coztitlmrc .gt_stub_row_group {
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

#coztitlmrc .gt_row_group_first td {
  border-top-width: 2px;
}

#coztitlmrc .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#coztitlmrc .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#coztitlmrc .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#coztitlmrc .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#coztitlmrc .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#coztitlmrc .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#coztitlmrc .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#coztitlmrc .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#coztitlmrc .gt_footnotes {
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

#coztitlmrc .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#coztitlmrc .gt_sourcenotes {
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

#coztitlmrc .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#coztitlmrc .gt_left {
  text-align: left;
}

#coztitlmrc .gt_center {
  text-align: center;
}

#coztitlmrc .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#coztitlmrc .gt_font_normal {
  font-weight: normal;
}

#coztitlmrc .gt_font_bold {
  font-weight: bold;
}

#coztitlmrc .gt_font_italic {
  font-style: italic;
}

#coztitlmrc .gt_super {
  font-size: 65%;
}

#coztitlmrc .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#coztitlmrc .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#coztitlmrc .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#coztitlmrc .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#coztitlmrc .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#coztitlmrc .gt_fraction_denominator {
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
<td class="gt_row gt_right">75.6</td>
<td class="gt_row gt_right">30.2</td>
<td class="gt_row gt_right">45.4</td>
<td class="gt_row gt_left">한다</td>
<td class="gt_row gt_right">195.3</td>
<td class="gt_row gt_right">267.2</td>
<td class="gt_row gt_right">-71.9</td></tr>
    <tr><td class="gt_row gt_left">우리</td>
<td class="gt_row gt_right">47.3</td>
<td class="gt_row gt_right">8.9</td>
<td class="gt_row gt_right">38.4</td>
<td class="gt_row gt_left">강화</td>
<td class="gt_row gt_right">34.7</td>
<td class="gt_row gt_right">101.2</td>
<td class="gt_row gt_right">-66.5</td></tr>
    <tr><td class="gt_row gt_left">제도</td>
<td class="gt_row gt_right">56.7</td>
<td class="gt_row gt_right">24.9</td>
<td class="gt_row gt_right">31.8</td>
<td class="gt_row gt_left">보장</td>
<td class="gt_row gt_right">28.4</td>
<td class="gt_row gt_right">77.2</td>
<td class="gt_row gt_right">-48.8</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">9.8</td>
<td class="gt_row gt_right">31.2</td>
<td class="gt_row gt_left">지원</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">50.6</td>
<td class="gt_row gt_right">-31.7</td></tr>
    <tr><td class="gt_row gt_left">변화</td>
<td class="gt_row gt_right">33.1</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">25.1</td>
<td class="gt_row gt_left">문화</td>
<td class="gt_row gt_right">11.0</td>
<td class="gt_row gt_right">42.6</td>
<td class="gt_row gt_right">-31.6</td></tr>
    <tr><td class="gt_row gt_left">마련</td>
<td class="gt_row gt_right">42.5</td>
<td class="gt_row gt_right">17.8</td>
<td class="gt_row gt_right">24.7</td>
<td class="gt_row gt_left">실현</td>
<td class="gt_row gt_right">12.6</td>
<td class="gt_row gt_right">42.6</td>
<td class="gt_row gt_right">-30.0</td></tr>
    <tr><td class="gt_row gt_left">는다</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">1.8</td>
<td class="gt_row gt_right">18.7</td>
<td class="gt_row gt_left">협력</td>
<td class="gt_row gt_right">6.3</td>
<td class="gt_row gt_right">35.5</td>
<td class="gt_row gt_right">-29.2</td></tr>
    <tr><td class="gt_row gt_left">권력</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">7.1</td>
<td class="gt_row gt_right">18.1</td>
<td class="gt_row gt_left">지역</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">49.7</td>
<td class="gt_row gt_right">-29.2</td></tr>
    <tr><td class="gt_row gt_left">모두</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">17.2</td>
<td class="gt_row gt_left">교육</td>
<td class="gt_row gt_right">28.4</td>
<td class="gt_row gt_right">55.9</td>
<td class="gt_row gt_right">-27.5</td></tr>
    <tr><td class="gt_row gt_left">개혁</td>
<td class="gt_row gt_right">22.1</td>
<td class="gt_row gt_right">8.0</td>
<td class="gt_row gt_right">14.1</td>
<td class="gt_row gt_left">사회</td>
<td class="gt_row gt_right">91.4</td>
<td class="gt_row gt_right">117.2</td>
<td class="gt_row gt_right">-25.8</td></tr>
    <tr><td class="gt_row gt_left">정치</td>
<td class="gt_row gt_right">31.5</td>
<td class="gt_row gt_right">17.8</td>
<td class="gt_row gt_right">13.7</td>
<td class="gt_row gt_left">구축</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">42.6</td>
<td class="gt_row gt_right">-23.7</td></tr>
    <tr><td class="gt_row gt_left">세대</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">5.3</td>
<td class="gt_row gt_right">13.6</td>
<td class="gt_row gt_left">국가</td>
<td class="gt_row gt_right">20.5</td>
<td class="gt_row gt_right">43.5</td>
<td class="gt_row gt_right">-23.0</td></tr>
    <tr><td class="gt_row gt_left">위한</td>
<td class="gt_row gt_right">41.0</td>
<td class="gt_row gt_right">27.5</td>
<td class="gt_row gt_right">13.5</td>
<td class="gt_row gt_left">에너지</td>
<td class="gt_row gt_right">1.6</td>
<td class="gt_row gt_right">24.0</td>
<td class="gt_row gt_right">-22.4</td></tr>
    <tr><td class="gt_row gt_left">기회</td>
<td class="gt_row gt_right">25.2</td>
<td class="gt_row gt_right">12.4</td>
<td class="gt_row gt_right">12.8</td>
<td class="gt_row gt_left">참여</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">24.9</td>
<td class="gt_row gt_right">-21.7</td></tr>
    <tr><td class="gt_row gt_left">10</td>
<td class="gt_row gt_right">14.2</td>
<td class="gt_row gt_right">2.7</td>
<td class="gt_row gt_right">11.5</td>
<td class="gt_row gt_left">발전</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">39.9</td>
<td class="gt_row gt_right">-21.0</td></tr>
  </tbody>
  
  
</table>
</div>
```


#### 문서 전반의 상대빈도

`tidylo`패키지의 `bind_log_odds()`함수로 계산하는 가중로그승산비를 이용.

- bind_log_odds(tbl, set, feature, n, uninformative = FALSE, unweighted = FALSE)
 -tbl: 정돈데이터(feature와 set이 하나의 행에 저장)
 -set: feature를 비교하기 위한 set(group)에 대한 정보(예: 긍정 vs. 부정)이 저장된 열
 - feature: feature(단어나 바이그램 등의 텍스트자료)가 저장된 열.
 - n: feature-set의 빈도를 저장한 열
 - uninformative: uninformative 디리슐레 분포 사용 여부. 기본값은 FALSE
 - unweighted: 비가중 로그승산 사용여부. 기본값은 FALSE. TRUE로 지정하면 비가중 로그승산비(log_odds) 열을 추가
 


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
<div id="tawynnmaqh" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#tawynnmaqh .gt_table {
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

#tawynnmaqh .gt_heading {
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

#tawynnmaqh .gt_title {
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

#tawynnmaqh .gt_subtitle {
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

#tawynnmaqh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tawynnmaqh .gt_col_headings {
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

#tawynnmaqh .gt_col_heading {
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

#tawynnmaqh .gt_column_spanner_outer {
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

#tawynnmaqh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#tawynnmaqh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#tawynnmaqh .gt_column_spanner {
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

#tawynnmaqh .gt_group_heading {
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

#tawynnmaqh .gt_empty_group_heading {
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

#tawynnmaqh .gt_from_md > :first-child {
  margin-top: 0;
}

#tawynnmaqh .gt_from_md > :last-child {
  margin-bottom: 0;
}

#tawynnmaqh .gt_row {
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

#tawynnmaqh .gt_stub {
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

#tawynnmaqh .gt_stub_row_group {
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

#tawynnmaqh .gt_row_group_first td {
  border-top-width: 2px;
}

#tawynnmaqh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#tawynnmaqh .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#tawynnmaqh .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#tawynnmaqh .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tawynnmaqh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#tawynnmaqh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#tawynnmaqh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#tawynnmaqh .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#tawynnmaqh .gt_footnotes {
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

#tawynnmaqh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#tawynnmaqh .gt_sourcenotes {
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

#tawynnmaqh .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#tawynnmaqh .gt_left {
  text-align: left;
}

#tawynnmaqh .gt_center {
  text-align: center;
}

#tawynnmaqh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#tawynnmaqh .gt_font_normal {
  font-weight: normal;
}

#tawynnmaqh .gt_font_bold {
  font-weight: bold;
}

#tawynnmaqh .gt_font_italic {
  font-style: italic;
}

#tawynnmaqh .gt_super {
  font-size: 65%;
}

#tawynnmaqh .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#tawynnmaqh .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#tawynnmaqh .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#tawynnmaqh .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#tawynnmaqh .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#tawynnmaqh .gt_fraction_denominator {
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
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_right">2.04</td>
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
<td class="gt_row gt_left">민주주의</td>
<td class="gt_row gt_right">17</td>
<td class="gt_row gt_right">2.37</td></tr>
    <tr><td class="gt_row gt_left">폐지</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">1.86</td>
<td class="gt_row gt_left">예술</td>
<td class="gt_row gt_right">16</td>
<td class="gt_row gt_right">2.29</td></tr>
    <tr><td class="gt_row gt_left">도록</td>
<td class="gt_row gt_right">48</td>
<td class="gt_row gt_right">1.79</td>
<td class="gt_row gt_left">상생</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">미래</td>
<td class="gt_row gt_right">26</td>
<td class="gt_row gt_right">1.68</td>
<td class="gt_row gt_left">으로써</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">경우</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">포용</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">1.90</td></tr>
    <tr><td class="gt_row gt_left">내일</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">당원</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">대통령</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">여성</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">동물</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">의료</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">1.81</td></tr>
    <tr><td class="gt_row gt_left">비리</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">촉진</td>
<td class="gt_row gt_right">9</td>
<td class="gt_row gt_right">1.72</td></tr>
    <tr><td class="gt_row gt_left">스스로</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">공동</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">앞장선다</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">소통</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">인간</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">임금</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
    <tr><td class="gt_row gt_left">자유민주주의</td>
<td class="gt_row gt_right">4</td>
<td class="gt_row gt_right">1.66</td>
<td class="gt_row gt_left">탄소</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">1.62</td></tr>
  </tbody>
  
  
</table>
</div>
```



### 감정어 빈도

#### 감정사전

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
# 열 결합
bind_cols(
  # 국민의 당
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
<div id="zjijtbysbo" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#zjijtbysbo .gt_table {
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

#zjijtbysbo .gt_heading {
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

#zjijtbysbo .gt_title {
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

#zjijtbysbo .gt_subtitle {
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

#zjijtbysbo .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zjijtbysbo .gt_col_headings {
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

#zjijtbysbo .gt_col_heading {
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

#zjijtbysbo .gt_column_spanner_outer {
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

#zjijtbysbo .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#zjijtbysbo .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#zjijtbysbo .gt_column_spanner {
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

#zjijtbysbo .gt_group_heading {
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

#zjijtbysbo .gt_empty_group_heading {
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

#zjijtbysbo .gt_from_md > :first-child {
  margin-top: 0;
}

#zjijtbysbo .gt_from_md > :last-child {
  margin-bottom: 0;
}

#zjijtbysbo .gt_row {
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

#zjijtbysbo .gt_stub {
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

#zjijtbysbo .gt_stub_row_group {
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

#zjijtbysbo .gt_row_group_first td {
  border-top-width: 2px;
}

#zjijtbysbo .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zjijtbysbo .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#zjijtbysbo .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#zjijtbysbo .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zjijtbysbo .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#zjijtbysbo .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#zjijtbysbo .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#zjijtbysbo .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#zjijtbysbo .gt_footnotes {
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

#zjijtbysbo .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zjijtbysbo .gt_sourcenotes {
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

#zjijtbysbo .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#zjijtbysbo .gt_left {
  text-align: left;
}

#zjijtbysbo .gt_center {
  text-align: center;
}

#zjijtbysbo .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#zjijtbysbo .gt_font_normal {
  font-weight: normal;
}

#zjijtbysbo .gt_font_bold {
  font-weight: bold;
}

#zjijtbysbo .gt_font_italic {
  font-style: italic;
}

#zjijtbysbo .gt_super {
  font-size: 65%;
}

#zjijtbysbo .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#zjijtbysbo .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#zjijtbysbo .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#zjijtbysbo .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#zjijtbysbo .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#zjijtbysbo .gt_fraction_denominator {
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
<td class="gt_row gt_right">45</td></tr>
    <tr><td class="gt_row gt_center">혁신</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">15</td>
<td class="gt_row gt_center">혁신</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">31</td></tr>
    <tr><td class="gt_row gt_center">발전</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">12</td>
<td class="gt_row gt_center">적극</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">27</td></tr>
    <tr><td class="gt_row gt_center">안전</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_center">안정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">22</td></tr>
    <tr><td class="gt_row gt_center">함께</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_center">위기</td>
<td class="gt_row gt_right">-1</td>
<td class="gt_row gt_right">21</td></tr>
    <tr><td class="gt_row gt_center">행복</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">10</td>
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
<td class="gt_row gt_center">안전</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">12</td></tr>
    <tr><td class="gt_row gt_center">존중</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">6</td>
<td class="gt_row gt_center">존중</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">11</td></tr>
    <tr><td class="gt_row gt_center">능력</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_center">소득</td>
<td class="gt_row gt_right">1</td>
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
<div id="wztyrkrquq" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#wztyrkrquq .gt_table {
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

#wztyrkrquq .gt_heading {
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

#wztyrkrquq .gt_title {
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

#wztyrkrquq .gt_subtitle {
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

#wztyrkrquq .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wztyrkrquq .gt_col_headings {
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

#wztyrkrquq .gt_col_heading {
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

#wztyrkrquq .gt_column_spanner_outer {
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

#wztyrkrquq .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#wztyrkrquq .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#wztyrkrquq .gt_column_spanner {
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

#wztyrkrquq .gt_group_heading {
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

#wztyrkrquq .gt_empty_group_heading {
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

#wztyrkrquq .gt_from_md > :first-child {
  margin-top: 0;
}

#wztyrkrquq .gt_from_md > :last-child {
  margin-bottom: 0;
}

#wztyrkrquq .gt_row {
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

#wztyrkrquq .gt_stub {
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

#wztyrkrquq .gt_stub_row_group {
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

#wztyrkrquq .gt_row_group_first td {
  border-top-width: 2px;
}

#wztyrkrquq .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wztyrkrquq .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#wztyrkrquq .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#wztyrkrquq .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wztyrkrquq .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wztyrkrquq .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#wztyrkrquq .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#wztyrkrquq .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wztyrkrquq .gt_footnotes {
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

#wztyrkrquq .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wztyrkrquq .gt_sourcenotes {
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

#wztyrkrquq .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wztyrkrquq .gt_left {
  text-align: left;
}

#wztyrkrquq .gt_center {
  text-align: center;
}

#wztyrkrquq .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#wztyrkrquq .gt_font_normal {
  font-weight: normal;
}

#wztyrkrquq .gt_font_bold {
  font-weight: bold;
}

#wztyrkrquq .gt_font_italic {
  font-style: italic;
}

#wztyrkrquq .gt_super {
  font-size: 65%;
}

#wztyrkrquq .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#wztyrkrquq .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#wztyrkrquq .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#wztyrkrquq .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#wztyrkrquq .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#wztyrkrquq .gt_fraction_denominator {
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
<td class="gt_row gt_right">152</td>
<td class="gt_row gt_left">57.6%</td>
<td class="gt_row gt_right">283</td>
<td class="gt_row gt_left">51.5%</td></tr>
    <tr><td class="gt_row gt_left">부정</td>
<td class="gt_row gt_right">83</td>
<td class="gt_row gt_left">31.4%</td>
<td class="gt_row gt_right">158</td>
<td class="gt_row gt_left">28.7%</td></tr>
    <tr><td class="gt_row gt_left">중립</td>
<td class="gt_row gt_right">29</td>
<td class="gt_row gt_left">11.0%</td>
<td class="gt_row gt_right">109</td>
<td class="gt_row gt_left">19.8%</td></tr>
    <tr><td class="gt_row gt_left">Total</td>
<td class="gt_row gt_right">264</td>
<td class="gt_row gt_left">100.0%</td>
<td class="gt_row gt_right">550</td>
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
<div id="pglkuwphiw" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#pglkuwphiw .gt_table {
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

#pglkuwphiw .gt_heading {
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

#pglkuwphiw .gt_title {
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

#pglkuwphiw .gt_subtitle {
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

#pglkuwphiw .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#pglkuwphiw .gt_col_headings {
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

#pglkuwphiw .gt_col_heading {
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

#pglkuwphiw .gt_column_spanner_outer {
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

#pglkuwphiw .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#pglkuwphiw .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#pglkuwphiw .gt_column_spanner {
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

#pglkuwphiw .gt_group_heading {
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

#pglkuwphiw .gt_empty_group_heading {
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

#pglkuwphiw .gt_from_md > :first-child {
  margin-top: 0;
}

#pglkuwphiw .gt_from_md > :last-child {
  margin-bottom: 0;
}

#pglkuwphiw .gt_row {
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

#pglkuwphiw .gt_stub {
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

#pglkuwphiw .gt_stub_row_group {
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

#pglkuwphiw .gt_row_group_first td {
  border-top-width: 2px;
}

#pglkuwphiw .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#pglkuwphiw .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#pglkuwphiw .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#pglkuwphiw .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#pglkuwphiw .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#pglkuwphiw .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#pglkuwphiw .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#pglkuwphiw .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#pglkuwphiw .gt_footnotes {
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

#pglkuwphiw .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#pglkuwphiw .gt_sourcenotes {
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

#pglkuwphiw .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#pglkuwphiw .gt_left {
  text-align: left;
}

#pglkuwphiw .gt_center {
  text-align: center;
}

#pglkuwphiw .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#pglkuwphiw .gt_font_normal {
  font-weight: normal;
}

#pglkuwphiw .gt_font_bold {
  font-weight: bold;
}

#pglkuwphiw .gt_font_italic {
  font-style: italic;
}

#pglkuwphiw .gt_super {
  font-size: 65%;
}

#pglkuwphiw .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#pglkuwphiw .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#pglkuwphiw .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#pglkuwphiw .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#pglkuwphiw .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#pglkuwphiw .gt_fraction_denominator {
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
<td class="gt_row gt_right">23.6</td>
<td class="gt_row gt_right">27.5</td></tr>
    <tr><td class="gt_row gt_left">발전</td>
<td class="gt_row gt_right">12</td>
<td class="gt_row gt_right">45</td>
<td class="gt_row gt_right">18.9</td>
<td class="gt_row gt_right">39.9</td></tr>
    <tr><td class="gt_row gt_left">안전</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">12</td>
<td class="gt_row gt_right">17.3</td>
<td class="gt_row gt_right">10.7</td></tr>
    <tr><td class="gt_row gt_left">함께</td>
<td class="gt_row gt_right">11</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">17.3</td>
<td class="gt_row gt_right">8.9</td></tr>
    <tr><td class="gt_row gt_left">행복</td>
<td class="gt_row gt_right">10</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">15.8</td>
<td class="gt_row gt_right">7.1</td></tr>
    <tr><td class="gt_row gt_left">안정</td>
<td class="gt_row gt_right">8</td>
<td class="gt_row gt_right">22</td>
<td class="gt_row gt_right">12.6</td>
<td class="gt_row gt_right">19.5</td></tr>
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
<div id="wllruxvwqa" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#wllruxvwqa .gt_table {
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

#wllruxvwqa .gt_heading {
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

#wllruxvwqa .gt_title {
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

#wllruxvwqa .gt_subtitle {
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

#wllruxvwqa .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wllruxvwqa .gt_col_headings {
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

#wllruxvwqa .gt_col_heading {
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

#wllruxvwqa .gt_column_spanner_outer {
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

#wllruxvwqa .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#wllruxvwqa .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#wllruxvwqa .gt_column_spanner {
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

#wllruxvwqa .gt_group_heading {
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

#wllruxvwqa .gt_empty_group_heading {
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

#wllruxvwqa .gt_from_md > :first-child {
  margin-top: 0;
}

#wllruxvwqa .gt_from_md > :last-child {
  margin-bottom: 0;
}

#wllruxvwqa .gt_row {
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

#wllruxvwqa .gt_stub {
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

#wllruxvwqa .gt_stub_row_group {
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

#wllruxvwqa .gt_row_group_first td {
  border-top-width: 2px;
}

#wllruxvwqa .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wllruxvwqa .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#wllruxvwqa .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#wllruxvwqa .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wllruxvwqa .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#wllruxvwqa .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#wllruxvwqa .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#wllruxvwqa .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#wllruxvwqa .gt_footnotes {
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

#wllruxvwqa .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wllruxvwqa .gt_sourcenotes {
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

#wllruxvwqa .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#wllruxvwqa .gt_left {
  text-align: left;
}

#wllruxvwqa .gt_center {
  text-align: center;
}

#wllruxvwqa .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#wllruxvwqa .gt_font_normal {
  font-weight: normal;
}

#wllruxvwqa .gt_font_bold {
  font-weight: bold;
}

#wllruxvwqa .gt_font_italic {
  font-style: italic;
}

#wllruxvwqa .gt_super {
  font-size: 65%;
}

#wllruxvwqa .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#wllruxvwqa .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#wllruxvwqa .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#wllruxvwqa .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#wllruxvwqa .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#wllruxvwqa .gt_fraction_denominator {
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
<td class="gt_row gt_right">18.6</td></tr>
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
<td class="gt_row gt_right">7</td>
<td class="gt_row gt_right">3.2</td>
<td class="gt_row gt_right">6.2</td></tr>
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

##### 문서 살펴보기

`crayon`패키지로 특정 단어을 색으로 표시해 어느 문장에 사용됐는지 탐색

먼저 문장 단위로 토큰화


```r
readRDS("ppp_v.rds") %>% 
  tibble(text = .) %>% 
  unnest_tokens(sentence, text, 
                token  = "regex", pattern = "\\.") -> ppp_st
readRDS("tmj_v.rds") %>% 
  tibble(text = .) %>% 
  unnest_tokens(sentence, text, 
                token  = "regex", pattern = "\\.") -> tmj_st
```


국민의당이 기술한 위기

```r
ppp_st %>% 
  filter(str_detect(sentence, "위기")) %>% 
  pull() -> ppp_txt

library(fansi)
library(glue)
library(crayon)
```

```
## 
## Attaching package: 'crayon'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     %+%
```

```r
options(crayon.enabled = TRUE)

crayon_words <- function(input_text, word = "위기") {

  replaced_text <- str_replace_all(input_text, word, "{red {word}}")

  for(i in 1:length(replaced_text)) {
    crayon_text <- glue::glue_col(deparse(replaced_text[[i]]))
    print(crayon_text)
  }
}
crayon_words(input_text = ppp_txt, "위기")
```

```
## " \r\n     \r\n 지금 우리는 세계질서의 대전환과 북한의 핵무장, 지구환경 변화와 거듭되고 있는 질병과 재난, 경제의 질적 변화로 인한 불확실성과 양극화의 심화, 인구절벽 등 중대한 [31m위기[39m 앞에 서 있다"
## " 국가적 [31m위기[39m 해결에 앞장서야 할 정치는 국민이 부여한 권한에 대한 책임과 역할을 다하지 못하고, 오히려 국민 분열을 조장하는 등 사회적 혼란과 함께 정치 불신을 심화 시켜 왔다"
## " 지방 소멸 [31m위기[39m에 대비하여 각 지역이 지속가능한 경쟁력을 갖추도록 하고 삶의 질을 획기적으로 향상한다"
## " 소득․지역․계층에 따른 격차 없이 질병에 대해 적절하게 치료를 받을 수 있는 권리를 보장하고, 감염병 등 공중보건 [31m위기[39m에 효과적으로 대처한다"
## " 나라와 국민을 위해 헌신하고 희생하신 분들은 물론 그 가족과 유족에 대해서는 정부가 끝까지 책임지고 보상과 지원을 하도록 국가의 책임을 강화하는 한편, 군인과 보훈가족이 사회적으로 존경받고 귀감이 되도록 예우를 하는 분[31m위기[39m를 조성해 나간다"
```


더불어민주당이 기술한 위기


```r
tmj_st %>% 
  filter(str_detect(sentence, "위기")) %>% 
  pull() -> tmj_txt
crayon_words(input_text = tmj_txt, "위기")
```

```
## " 4차 산업혁명 시대의 디지털 전환과 기후[31m위기[39m로 인한 미래 불확실성이 증대하고 있으며, 팬데믹 이후 전세계적 경제[31m위기[39m는 국민의 삶을 위협하고 사회‧경제적 불안정을 가중시키고 있다"
## " 디지털 전환, 기후[31m위기[39m 대응, 탄소중립 실현 등 대전환 시대에 경제적 생산성과 사회적 지속가능성 간의 선순환을 추구하고, 포용성장을 통해 국민 개개인의 역량이 발현될 수 있는 사회적 여건을 구축한다"
## " 금융혁신으로 산업 경쟁력과 실물지원 기능을 향상시키고, 불안정한 국제금융질서와 금융[31m위기[39m에 대응하며, 금융시장의 견제와 균형을 회복할 수 있는 관리·감독체계를 마련하여 건전성과 공공성을 확보한다"
## " 기후[31m위기[39m에 대응하기 위한 에너지 전환을 지속 추진하고 합리적인 탄소중립전략을 수립한다"
## " 농수축협의 활성화, 가격안정을 위한 수급균형과 유통구조 개선, 탄소중립 실현 및 지역순환형 생산소비 시스템 구축 등을 통해 식량자급을 달성하고 기후[31m위기[39m에 대비한다"
## " 경제[31m위기[39m를 빌미로 한 무분별한 정리해고의 남용을 방지한다"
## " 인구감소와 고령화로 인한 지방소멸의 [31m위기[39m를 극복하기 위해 도시재생 및 농‧산‧어촌의 회생을 추진한다"
## " 이를 통해 저출생·고령화, 지방소멸 [31m위기[39m, 지역 양극화를 근본적으로 치유할 수 있는 기반을 강화한다"
## "     국민 안전을 책임지는 튼튼한 안보 한반도의 지속적인 평화와 국민 안전을 지키기 위해 신속하고 효율적인 국가[31m위기[39m관리체계를 구축한다"
## " 인류의 보편적 가치인 인권, 민주주의 구현과 함께 평화, 반테러, 비핵화, 기후 및 감염병 [31m위기[39m 대응 등을 실현하기 위해 국제사회와 적극 협력하고, 글로벌 선도국가로서의 위상을 확립해 나간다"
## " 기후[31m위기[39m 대응, 보건의료협력 등 시대적 요구에 부합하는 남북협력 의제를 설정하여, 평화경제의 새로운 기회를 만들고 남북 공동번영을 도모한다"
## " 기후·에너지·환경\n                            \n                            지구생태계의 회복과 보전, 특히 기후 [31m위기[39m에 대한 적극 대응과 탄소중립 실현을 위해 노력한다"
## " 에너지 취약계층의 에너지 복지를 확대하고 글로벌 공급망 [31m위기[39m대응을 위해 에너지 안보를 제고한다"
## "     지속가능한 에너지 전환 달성 기후[31m위기[39m 해결을 위한 핵심 수단이자 에너지 수급의 지속가능성을 높이는 에너지 전환을 지속적으로 추진한다"
## "      기후[31m위기[39m 대응과 탄소중립사회 구현기후[31m위기[39m에 적극 대응하며 탄소중립사회로의 신속한 전환을 위해 노력한다"
## " 기후[31m위기[39m로 심화되는 불평등과 에너지 빈곤문제 해결을 위해 노력한다"
## " 미세먼지, 황사 등 동북아시아 환경문제 해결을 위해 국가 간 협력을 강화하고, 국제사회의 책임 있는 일원으로서 기후[31m위기[39m에 선제적으로 대응한다"
## " 팬데믹 시대의 복합적 [31m위기[39m를 극복하고 민주시민 역량을 가진 창의적 인재를 육성하기 위해 교육대전환을 실현한다"
## " 기후 변화와 지속적인 감염병 등 다양한 [31m위기[39m로부터 초래된 교육결손을 극복하기 위한 지원을 확대한다"
## " 지방대 [31m위기[39m 대응을 위해 지자체·대학·산업체 등이 자원을 결집하여 대학의 교육을 내실화한다"
```





#### 상대빈도


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
## [1m[22mNew names:
## [36m•[39m `word` -> `word...1`
## [36m•[39m `n` -> `n...2`
## [36m•[39m `log_odds_weighted` -> `log_odds_weighted...3`
## [36m•[39m `word` -> `word...4`
## [36m•[39m `n` -> `n...5`
## [36m•[39m `log_odds_weighted` -> `log_odds_weighted...6`
```

```{=html}
<div id="fexacnbkex" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#fexacnbkex .gt_table {
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

#fexacnbkex .gt_heading {
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

#fexacnbkex .gt_title {
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

#fexacnbkex .gt_subtitle {
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

#fexacnbkex .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#fexacnbkex .gt_col_headings {
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

#fexacnbkex .gt_col_heading {
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

#fexacnbkex .gt_column_spanner_outer {
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

#fexacnbkex .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#fexacnbkex .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#fexacnbkex .gt_column_spanner {
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

#fexacnbkex .gt_group_heading {
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

#fexacnbkex .gt_empty_group_heading {
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

#fexacnbkex .gt_from_md > :first-child {
  margin-top: 0;
}

#fexacnbkex .gt_from_md > :last-child {
  margin-bottom: 0;
}

#fexacnbkex .gt_row {
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

#fexacnbkex .gt_stub {
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

#fexacnbkex .gt_stub_row_group {
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

#fexacnbkex .gt_row_group_first td {
  border-top-width: 2px;
}

#fexacnbkex .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#fexacnbkex .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#fexacnbkex .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#fexacnbkex .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#fexacnbkex .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#fexacnbkex .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#fexacnbkex .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#fexacnbkex .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#fexacnbkex .gt_footnotes {
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

#fexacnbkex .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#fexacnbkex .gt_sourcenotes {
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

#fexacnbkex .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#fexacnbkex .gt_left {
  text-align: left;
}

#fexacnbkex .gt_center {
  text-align: center;
}

#fexacnbkex .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#fexacnbkex .gt_font_normal {
  font-weight: normal;
}

#fexacnbkex .gt_font_bold {
  font-weight: bold;
}

#fexacnbkex .gt_font_italic {
  font-style: italic;
}

#fexacnbkex .gt_super {
  font-size: 65%;
}

#fexacnbkex .gt_two_val_uncert {
  display: inline-block;
  line-height: 1em;
  text-align: right;
  font-size: 60%;
  vertical-align: -0.25em;
  margin-left: 0.1em;
}

#fexacnbkex .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#fexacnbkex .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#fexacnbkex .gt_slash_mark {
  font-size: 0.7em;
  line-height: 0.7em;
  vertical-align: 0.15em;
}

#fexacnbkex .gt_fraction_numerator {
  font-size: 0.6em;
  line-height: 0.6em;
  vertical-align: 0.45em;
}

#fexacnbkex .gt_fraction_denominator {
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
<td class="gt_row gt_left">장애인</td>
<td class="gt_row gt_right">5</td>
<td class="gt_row gt_right">1.32</td></tr>
    <tr><td class="gt_row gt_left">파괴</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.42</td>
<td class="gt_row gt_left">남용</td>
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
<td class="gt_row gt_right">1.16</td>
<td class="gt_row gt_left">혐오</td>
<td class="gt_row gt_right">3</td>
<td class="gt_row gt_right">1.02</td></tr>
    <tr><td class="gt_row gt_left">범죄</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1.16</td>
<td class="gt_row gt_left">자긍심</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">0.83</td></tr>
    <tr><td class="gt_row gt_left">재능</td>
<td class="gt_row gt_right">2</td>
<td class="gt_row gt_right">1.16</td>
<td class="gt_row gt_left">발전</td>
<td class="gt_row gt_right">45</td>
<td class="gt_row gt_right">0.61</td></tr>
    <tr><td class="gt_row gt_left">가난</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">개성</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">명예</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">기대</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">부조리</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">두려움</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">원동력</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">부정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">인정</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">불법</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">전문가</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">불안</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">정상</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">비판</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">존경</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">수익</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
    <tr><td class="gt_row gt_left">지나친</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.82</td>
<td class="gt_row gt_left">아픔</td>
<td class="gt_row gt_right">1</td>
<td class="gt_row gt_right">0.59</td></tr>
  </tbody>
  
  
</table>
</div>
```



