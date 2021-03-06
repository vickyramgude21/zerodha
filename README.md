# zerodha
<html>
   <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
      <meta name="description" content="Zerodha - Bhavcopy Analysis">
      <meta name="author" content="vicky">
      <title>Zerodha - Bhavcopy Analysis</title>
      <link rel="stylesheet" href="/static/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
      <link rel="stylesheet" href="/static/css/pagination.css" >
   </head>
   <body>
      <div class="text-center" style="margin-bottom:0;border-bottom: solid 2px #ff5722;padding: 25px;background-color: #fbfbfb;">
         <h1 style="color:#ff5722;">Zerodha - Equity bhavcopy analysis</h1>
         <p style="font-size: 14px;">BSE publishes a "Bhavcopy" file every day <a href="https://www.bseindia.com/markets/MarketInfo/BhavCopy.aspx">here</a>
             </p>
      </div>
      <div class="container" ng-app="web-app" ng-controller="form-controller"  ng-init="setupApp();" style="margin-top:30px">
         <div class="row">
            <div class="col-sm-4">
               <h2 style="color:#ff5722;">About Implementation</h2>
               <ul>
                  <li>Data has been stored in redis with two data structures. <b>1. Hashes  2. Sorted Sets</b></li>
                  <li><b>Hashes:</b> It stores whole object of stock with key format {STOCK_CODE}:{STOCK_NAME}</li>
                  <li><b>Sorted Sets:</b> It stores same key format {STOCK_CODE}:{STOCK_NAME} with attached score.
                     Score logic is : round(((value['close'] - value['open']) / value['open']) * 100, 2)
                  </li>
                  <li>
                     <b>Assumptions:</b> Assuming logic of top stock is "Highest positive percentage change first."
                  </li>
                  <li>
                     <b>Top Stocks:</b> Query sored-set with "ZREVRANGE key start stop" to get a keys for Hashmap and then get data from Hashmap with "HGETALL key".
                  </li>
                  <li>
                     <b>Name Search:</b> Use "SCAN" function to query with pattern and find keys for Hashmap and then get data from Hashmap with "HGETALL key".
                  </li>
                 
               </ul>
               
               <ul class="nav nav-pills flex-column">
                 
                  
               </ul>
               <hr class="d-sm-none">
            </div>
            <div class="col-sm-8">
               <h2 style="color:#ff5722;">TOP STOCKS</h2>
               <p class="h5" style="">Equity Bhavcopy of {{ eq_bhav_date }} <button data-ng-click="loadLatestData()" type="button" class="btn btn-link">Click to load latest</button></p>
               <table class="table">
                  <thead>
                     <tr>
                        <th scope="col">Code</th>
                        <th scope="col">Name</th>
                        <th scope="col">Open</th>
                        <th scope="col">High</th>
                        <th scope="col">Low</th>
                        <th scope="col">Close</th>
                        <th scope="col">% Change</th>
                     </tr>
                  </thead>
                  <tbody>
                     <tr ng-repeat="row in top_stocks">
                        <td>{{ row.code }}</td>
                        <td>{{ row.name }}</td>
                        <td>{{ row.open }}</td>
                        <td>{{ row.high }}</td>
                        <td>{{ row.low }}</td>
                        <td>{{ row.close }}</td>
                        <td>{{ row.percentage }}</td>
                     </tr>
                     <tr data-ng-if="top_stocks.length == 0" >
                        <td colspan="7">No data found</td>
                     </tr>
                  </tbody>
               </table>
               <div id="paginations">
               </div>
               <br/> <br/>
               <h2 style="color:#ff5722;">SEARCH STOCKS</h2>
               <br/>
               <div class="input-group mb-3    ">
                  <input type="text" ng-keypress="checkEnter($event)" data-ng-model="search_text" class="form-control" placeholder="Stock's name" aria-describedby="button-addon2">
                  <div class="input-group-append">
                     <button data-ng-click="getStockByName()" class="btn btn-outline-secondary" type="button" id="button-addon2">Search</button>
                  </div>
               </div>
               <table class="table">
                  <thead>
                     <tr>
                        <th scope="col">Code</th>
                        <th scope="col">Name</th>
                        <th scope="col">Open</th>
                        <th scope="col">High</th>
                        <th scope="col">Low</th>
                        <th scope="col">Close</th>
                        <th scope="col">% Change</th>
                     </tr>
                  </thead>
                  <tbody>
                     <tr ng-repeat="row in search_stocks">
                        <td>{{ row.code }}</td>
                        <td>{{ row.name }}</td>
                        <td>{{ row.open }}</td>
                        <td>{{ row.high }}</td>
                        <td>{{ row.low }}</td>
                        <td>{{ row.close }}</td>
                        <td>{{ row.percentage }}</td>
                     </tr>
                     <tr data-ng-if="search_stocks.length == 0" >
                        <td colspan="7">No data found</td>
                     </tr>
                  </tbody>
               </table>
            </div>
         </div>
      </div>
      <br/><br/>
      <div class="jumbotron text-center" style="margin-bottom:0;padding: 0px;">
         <p style="margin-bottom: 0rem;">Built for zerodha Author: <a href="https://linkedin.com/in/jayjayswal/" target="_blank">vicky</a></p>
      </div>
      <script src="/static/js/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
      <script src="/static/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
      <script src="/static/js/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
      <script src="/static/js/angular.min.js" ></script>
      <script src="/static/js/pagination.min.js" ></script>
      
      <script src="/static/js/app.js"></script>
   </body>
</html>
