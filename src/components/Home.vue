<template>
  <div class="container">
    <div class="row">
      <div class="col-md-4">
        <div class="row multi-switcher">
          <div v-on:click="changeCurrency('USD')" class="col-4 first " :class="currency=='USD' ?  'active' : ''">USD</div>
          <div v-on:click="changeCurrency('EUR')" class="col-4" :class="currency=='EUR' ?  'active' : ''">EUR</div>
          <div v-on:click="changeCurrency('CAD')" class="col-4 last" :class="currency=='CAD' ?  'active' : ''">CAD</div>
        </div>
      </div>
      <div class="col-md-4">
        <div class="row multi-switcher">
          <div v-on:click="changeYears(5)"  class="col-4 first" :class="years.includes(5) ?  'active' : ''">5 YRS</div>
          <div v-on:click="changeYears(10)" class="col-4" :class="years.includes(10) ?  'active' : ''">10 YRS</div>
          <div v-on:click="changeYears(40)" class="col-4 last" :class="years.includes(40) ?  'active' : ''">40 YRS</div>
        </div>
      </div>
      <div class="col-md-4">
        <div class="row multi-switcher">
          <div v-on:click="changeDisplay('Spread')" class="col-4 first" :class="display=='Spread' ?  'active' : ''">Spread</div>
          <div v-on:click="changeDisplay('Yield')"  class="col-4" :class="display=='Yield' ?  'active' : ''">Yield</div>
          <div v-on:click="changeDisplay('3MLSpread')"  class="col-4 last" :class="display=='3MLSpread' ?  'active' : ''">3MLSpread</div>
        </div>
      </div>
    </div>
    <div class="row mt-4">
      <div class="col-md-12 pl-0">
        <div class="form-group">
          <input type="text" class="form-control" placeholder="Filter By Company Name" id="search-box" v-model="search"/>
        </div>
      </div>
    </div>
    <div class="row mt-3 mb-3">
      <div class="col-md-12 table-container">
        <div class="row table-header">
          <div class="col-3">
            Date Sent
            <span v-on:click="checkSortByDateSent()" class="px-2" :class="`sort-${sortByDateSent}`"><i  :class="`fas fa-sort-${sortByDateSent}`"></i></span>
          </div>
          <div class="col-3">
            Company
            <span v-on:click="checkSortByCompany()" class="px-2" :class="`sort-${sortByCompany}`"><i :class="`fas fa-sort-${sortByCompany}`"></i></span>
          </div>
          <div class="col-6">
            <div class="row">
              <div class="col-4 p-0" v-show="years.includes(5)">
                <p class="mb-0">5 Years</p>
                <div class="float-2-columns">Fix</div>
                <div class="float-2-columns">FRN</div>
              </div>
              <div class="col-4 p-0" v-show="years.includes(10)">
                <p class="mb-0">10 Years</p>
                <div class="float-2-columns">Fix</div>
                <div class="float-2-columns">FRN</div>
              </div>
              <div class="col-4 p-0" v-show="years.includes(40)">
                <p class="mb-0">40 Years</p>
                <div class="float-2-columns">Fix</div>
                <div class="float-2-columns">FRN</div>
              </div>
            </div>
          </div>
        </div>
        <div class="row" v-for="(item,filteredListIndex) in filteredList" :key="filteredListIndex">
          <div class="col-12">
            <div class="row main pt-2 pb-2">
              <div class="col-3">
                <span v-if="item.DateSent"><i class="fa fa-chevron-right" v-on:click="expandRows(filteredListIndex)" :id="`chevron-right-${filteredListIndex}`" aria-hidden="true"></i></span>
                <span v-if="item.DateSent"><i class="fa fa-chevron-down d-none" v-on:click="collapseRows(filteredListIndex)" :id="`chevron-down-${filteredListIndex}`" aria-hidden="true"></i></span>
                {{ changeDateFormat(item.DateSent) }}
              </div>
              <div class="col-3" v-html="'<strong>'+item.Company+'</strong>'">
              </div>
              <div class="col-6">
                <div class="row">
                  <div class="col-4 p-0" v-show="years.includes(5)">
                    <div class="" v-for="(quote,i) in item.Quote" :key="i">
                      <div class="" v-if="quote.Years==5  && quote[display]">
                        <div class="float-2-columns" v-if="quote.CouponType == 'FIX'">
                          <div :class="checkMin(5,quote[display],'FIX')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quote[display]+'bp' : quote[display]+'%' "></div>
                        </div>
                        <div class="float-2-columns" v-if="quote.CouponType=='FRN'">
                          <div :class="checkMin(5,quote[display],'FRN')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quote[display]+'bp' : quote[display]+'%' "></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(10)">
                    <div v-for="(quoteForten,tenIndex) in item.Quote" :key="tenIndex">
                      <div v-if="quoteForten.Years==10 && quoteForten[display]">
                        <div class="float-2-columns" v-if="quoteForten.CouponType=='FIX'">
                          <div :class="checkMin(10,quoteForten[display],'FIX')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quoteForten[display]+'bp' : quoteForten[display]+'%' "></div>
                        </div>
                        <div class="float-2-columns" v-if="quoteForten.CouponType=='FRN'">
                          <div :class="checkMin(10,quoteForten[display],'FRN')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quoteForten[display]+'bp' : quoteForten[display]+'%' "></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(40)">
                    <div v-for="(quoteForForty,fifteenIndex) in item.Quote" :key="fifteenIndex">
                      <div v-if="quoteForForty.Years==40  && quoteForForty[display]">
                        <div class="float-2-columns" v-if="quoteForForty.CouponType=='FIX'">
                          <div :class="checkMin(40,quoteForForty[display],'FIX')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quoteForForty[display]+'bp' : quoteForForty[display]+'%' "></div>
                        </div>
                        <div class="float-2-columns" v-if="quoteForForty.CouponType=='FRN'">
                          <div :class="checkMin(40,quoteForForty[display],'FRN')" v-html="display=='Spread' || display=='3MLSpread' ? '+'+quoteForForty[display]+'bp' : quoteForForty[display]+'%' "></div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>


            <div :class="`row minor minor-${filteredListIndex} d-none pt-2 pb-2`" v-for="(displayOption,index) in remainingDisplayOptions" :key="index">
              <div class="col-3">
              </div>
              <div class="col-3">
                  {{displayOption}}
              </div>
              <div class="col-6">
                <div class="row">
                  <div class="col-4 p-0" v-show="years.includes(5)">
                    <div class="loop" v-for="(quote,i) in item.Quote" :key="i">
                      <div class="" v-if="quote.Years==5 && quote[displayOption]">
                        <div class="float-2-columns" v-if="quote.CouponType == 'FIX'">
                          <div class="" v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quote[displayOption]+'bp' : quote[displayOption]+'%'"></div>
                        </div>
                        <div class="float-2-columns" v-if="quote.CouponType=='FRN'">
                          <div class=""  v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quote[displayOption]+'bp' : quote[displayOption]+'%'"></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(10)">
                    <div v-for="(quoteForten,tenIndex) in item.Quote" :key="tenIndex">
                      <div v-if="quoteForten.Years==10 && quoteForten[displayOption]">
                        <div class="float-2-columns" v-if="quoteForten.CouponType=='FIX'">
                          <div  v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quoteForten[displayOption]+'bp' : quoteForten[displayOption]+'%'"></div>
                        </div>
                        <div class="float-2-columns" v-if="quoteForten.CouponType=='FRN'">
                          <div  v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quoteForten[displayOption]+'bp' : quoteForten[displayOption]+'%'"></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(40)">
                    <div v-for="(quoteForForty,fortyIndex) in item.Quote" :key="fortyIndex">
                      <div v-if="quoteForForty.Years==40 && quoteForForty[displayOption]">
                        <div class="float-2-columns" v-if="quoteForForty.CouponType=='FIX'">
                          <div  v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quoteForForty[displayOption]+'bp' : quoteForForty[displayOption]+'%'"></div>
                        </div>
                        <div class="float-2-columns" v-if="quoteForForty.CouponType=='FRN'">
                          <div  v-html="displayOption=='Spread' || displayOption=='3MLSpread' ? '+'+quoteForForty[displayOption]+'bp' : quoteForForty[displayOption]+'%'"></div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <!--        start last row-->
        <div class="row">
          <div class="col-12">
            <div class="row main pt-2 pb-2">
              <div class="col-3">
              </div>
              <div class="col-3">
                Average By {{display}}
              </div>
              <div class="col-6">
                <div class="row">
                  <div class="col-4 p-0" v-show="years.includes(5)">
                    <div class="" >
                      <div class="">
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(5,'FIX')"></div>
                        </div>
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(5,'FRN')" ></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(10)">
                    <div >
                      <div >
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(10,'FIX')"></div>
                        </div>
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(10,'FRN')" ></div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="col-4 p-0" v-show="years.includes(40)">
                    <div >
                      <div >
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(40,'Fix')"></div>
                        </div>
                        <div class="float-2-columns" >
                          <div v-html="FixAverageByDisplay(40,'FNR')" ></div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <!--end last row-->
      </div>
    </div>
  </div>
</template>

<script>
import data from "./../../data.json";

export default {
  name: 'Home',
  data() {
    return {
      search:null,
      data: data,
      currency: 'USD',
      years: [5, 10, 40],
      display: 'Spread',
      sortByDateSent:'down',
      sortByCompany:'down',
    }
  },
  mounted() {

  },
  computed: {
    filteredList() {
      let dataItems=this.data.Items;
      let items=[];
      let curr=this.currency;
      dataItems.filter(function (n,i) {
        let quote = [];
        if(n.Quote!=null){
          quote = n.Quote.filter(function (m,j) {
            return m.Currency === this;
          },this);
        }
        items.push({
          'DateSent': n.DateSent,
          'Company': n.Company,
          'Id': n.Id,
          'Preferred': n.Preferred,
          'Quote': Object.values(quote)
        });

      },curr);

      if(this.search){
        items= items.filter(item => {
          return item.Company.toLowerCase().includes(this.search.toLowerCase())
        });
      }
      if(this.sortByCompany){
        if(this.sortByCompany=='down'){
          items=items.sort((a, b) => (a.Company < b.Company) ? 1 : -1)
        }else{
          items=items.sort((a, b) => (a.Company > b.Company) ? 1 : -1)
        }
      }
      if(this.sortByDateSent){
        if(this.sortByDateSent=='down'){
          items=items.sort((a, b) => ( new Date(a.DateSent) < new Date(b.DateSent)) ? 1 : -1)
        }else{
          items=items.sort((a, b) => (new Date(a.DateSent) > new Date(b.DateSent)) ? 1 : -1)
        }
      }

      return items;
    },
    remainingDisplayOptions(){
      let availableDisplayOptions=['Spread','Yield','3MLSpread'];
      let index=availableDisplayOptions.indexOf(this.display);
      let remainingOptions=[];
      if (index > -1) {
        remainingOptions=availableDisplayOptions.splice(index,1);
      }
      return availableDisplayOptions;
    }
  },
  methods: {
    changeCurrency(newCurrency) {
      this.currency = newCurrency;
    },
    changeYears(year) {
      if(this.years.includes(year)){
        const index=this.years.indexOf(year);
        let arr=this.years;
        arr.splice(index,1);
        this.years=arr;
      }else{
        this.years.push(year);
      }

    },
    changeDisplay(displayValue) {
      this.display = displayValue;
    },
    changeDateFormat(DateSent){
      if(DateSent){
        const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
        const formattedDate = new Date(DateSent);
        const yy = formattedDate.getFullYear().toString().slice(-2);
        let mm = monthNames[formattedDate.getMonth()]; // Months start at 0!
        let dd = formattedDate.getDate();
        if (dd < 10) dd = '0' + dd;
        if (mm < 10) mm = '0' + mm;
        return dd + '-' + mm + '-' + yy;
      }else{
        return '';
      }
    },
    expandRows(filteredListIndex){
      $('#chevron-right-'+filteredListIndex).addClass('d-none');
      $('#chevron-down-'+filteredListIndex).removeClass('d-none');
      $('.minor-'+filteredListIndex).removeClass('d-none');
    },
    collapseRows(filteredListIndex){
      $('#chevron-right-'+filteredListIndex).removeClass('d-none');
      $('#chevron-down-'+filteredListIndex).addClass('d-none');
      $('.minor-'+filteredListIndex).addClass('d-none');
    },
    FixAverageByDisplay(year,type){
          let total=0;
          let counter=0;
          for(let i=0; i < this.filteredList.length;i++){
            for(let j=0; j < this.filteredList[i].Quote.length;j++){
                if(this.filteredList[i].Quote[j].Years==year && this.filteredList[i].Quote[j].CouponType==type){
                  if(this.filteredList[i].Quote[j][this.display]){
                    total+=this.filteredList[i].Quote[j][this.display];
                    counter+=1;
                  }
                }
            }
          }
          if(total==0 || counter==0){
            return '';
      }
          if(this.display=='Spread' || this.display=='3MLSpread'){
            if((total/counter) % 1 != 0){
              return '+'+(total/counter).toFixed(2)+'bp';
            }
            return '+'+total/counter+'bp';
          }else{
            if((total/counter) % 1 != 0){
              return (total/counter).toFixed(3)+'%';
            }
            return total/counter+'%';
          }
    },
    checkMin(year,value,type){
      let arr=[];
      for(let i=0; i < this.filteredList.length;i++){
        for(let j=0; j < this.filteredList[i].Quote.length;j++){
          if(this.filteredList[i].Quote[j].Years==year && this.filteredList[i].Quote[j].CouponType==type){
            if(this.filteredList[i].Quote[j][this.display]){
                arr.push(this.filteredList[i].Quote[j][this.display]);
            }
          }
        }
      }
      const min= Math.min.apply(Math, arr);
      if(min==value){
        return 'highlight';
      }
      return '';
    },
    checkSortByDateSent(){
      if(this.sortByDateSent=='down'){
        this.sortByDateSent='up';
      }else{
        this.sortByDateSent='down';
      }
    },
    checkSortByCompany(){
      if(this.sortByCompany=='down'){
        this.sortByCompany='up';
      }else{
        this.sortByCompany='down';
      }
    }

  }

}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import '../assets/scss/styles.css';
</style>
