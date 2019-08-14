<https://www.taniarascia.com/how-to-use-the-javascript-fetch-api-to-get-json-data/>
<https://www.taniarascia.com/how-to-connect-to-an-api-with-javascript/>
<https://medium.freecodecamp.org/here-is-the-most-popular-ways-to-make-an-http-request-in-javascript-954ce8c95aaa>

<https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Third_party_APIs>


<https://blog.miguelgrinberg.com/post/writing-a-javascript-rest-client>


<https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data>


<https://css-tricks.com/using-fetch/> \
<https://www.sitepoint.com/xmlhttprequest-vs-the-fetch-api-whats-best-for-ajax-in-2019/> \
<https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch> \
<https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data> \
<https://stackoverflow.com/questions/35549547/what-is-the-difference-between-the-fetch-api-and-xmlhttprequest> \
<https://medium.com/@shahata/why-i-wont-be-using-fetch-api-in-my-apps-6900e6c6fe78> 

```
<script>
function json2table(json, classes){
  console.log("inside json2table");
  console.log(json)
  name="tasks"
  var cols = Object.keys(json[name][0]);   // keys from 1st object in array
  var headerRow = '';
  var bodyRows = '';
  classes = classes || '';

  cols.map(function(col) {
       headerRow += '<th>' + col + '</th>';
   });

  json[name].map(function(row) {
       bodyRows += '<tr>';
       //  Loop over object properties and create cells
       cols.map(function(colName) {
            bodyRows += '<td>' + row[colName] + '<td>';
       });
      bodyRows += '</tr>';
   });

  return '<table class=' +
       classes +
       '><thead><tr>' +
       headerRow +
       '</tr></thead><tbody>' +
       bodyRows +
       '</tbody></table>';
};
/********************/
function myFunc() {
 alert("myFunc Started");
 url="http://localhost:5000/todo/api/v1.0/tasks"
 fetch(url)
  .then(response => {
    console.log(response.status)
    if (response.ok) {
      console.log("OK")
      console.log(response)
      return response.json()
    } else {
      console.log("ERR")
           /* 
             alert("myFunc ERROR");
             return Promise.reject({
                   status: response.status,
                  statusText: response.statusText
              })
           */
    }
  });

  .then(data => {
      console.log("data:")
      console.log(data);
      table = json2table(data);
      document.getElementById('tableHolder').innerHTML=table;
  })
  .catch(error => {
    console.log("ERR code "+ error.status)
  });

}  
</script>  
```
