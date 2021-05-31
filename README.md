# inject

### 1. get search engine ID and google api key https://developers.google.com/custom-search/v1/introduction
### 2. go to https://www.googleapis.com/customsearch/v1/ by chrome
### 3. create new snippet by devtool
### 4. copy inject.js to new snippet
```JavaScript
let key = ''
let cx = ''
function injectUi() {
  document.body.innerHTML = `
    <h1>Custom Search JSON API</h1>
    <label for="keyword">input keyword:</label>
    <input type="text" id="keyword" name="keyword"><br><br>
    <button type="button" onclick="clean()">Clean</button>
    <button type="button" onclick="search()">Search</button><br><br> 
    <textarea id="content" rows="10" cols="100"></textarea><br><br>
    <button type="button" onclick="download()">download csv</button><br><br>
    <div id="link"></div>
    `
    clean()
}
function json2csv(res){
  for(const item of res.items){
    content.innerHTML += item.snippet.split('...')[0] + ","
    content.innerHTML += item.displayLink + ","
    content.innerHTML += item.htmlTitle.replace("<b>","").replace("</b>","") + ","
    content.innerHTML += item.link + "\r\n"
  }
}
function download(){
  let csv = document.getElementById("content").value
  let uri = 'data:text/csv;charset=utf-8,\uFEFF' + encodeURI(csv);
  let link = document.createElement("A")
  link.href = uri
  link.download = "test" + ".csv";
  link.click()
}

function clean(){
  let content = document.getElementById("content")
   content.innerHTML = "時間,來源,標題,連結\r\n"
   document.getElementById("keyword").value = ''
}

function search(startIndex = 1){
  let query = document.getElementById("keyword").value
   fetch(`https://www.googleapis.com/customsearch/v1/?key=${key}&cx=${cx}&q=${query}&start=${startIndex}&sort=date`)
    .then(res => res.json())
    .then(data => {
        json2csv(data)
        if(data.queries.nextPage[0].startIndex){
          if(data.queries.nextPage[0].startIndex < 100)
            search(data.queries.nextPage[0].startIndex)
          else
            return  
        }
      })
}

injectUi();



```
### 5. change key and cx
### 6. Ctrl + Enter run




##### reference:
  * https://developers.google.com/custom-search/v1/introduction
  * https://ithelp.ithome.com.tw/articles/10223751
