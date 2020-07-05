<template>
<f7-app :params="f7params" >
<!-- Left panel with cover effect-->
<f7-panel left cover>
    <f7-view>
        <f7-page>
            <f7-block id="menulogo">
                <div class="logo"></div>
            </f7-block>
            <f7-list inset>
                <f7-list-button v-for="menu in menus" :tab-link="menu.href">
                    <f7-icon :f7="menu.icon"></f7-icon> &nbsp; {{ menu.title }}
                    <f7-badge v-if="menu.badgeheart && isheart" color="pink">{{ heart_list.length }}</f7-badge>
                    <f7-badge v-if="menu.badgecart && iscart" color="pink">{{ cart_list.length }}</f7-badge>
                </f7-list-button>
                <f7-list-button @click="exitapp()"><f7-icon f7="power"></f7-icon> &nbsp; Выход</f7-list-button>
            </f7-list>
        </f7-page>
    </f7-view>
</f7-panel>
<!-- End Left panel -->
<div id="uuid" ref="uuid" data-id="2a3833cdee151f32"></div>
<f7-navbar :sliding="false">
    <f7-nav-left>
        <f7-link v-if="!active_arrow" icon-ios="f7:menu" icon-aurora="f7:menu" icon-md="material:menu" panel-open="left"></f7-link>
        <f7-link v-if="active_arrow" icon-ios="f7:arrow_back" icon-aurora="f7:arrow_back" icon-md="material:arrow_back" tab-link="#tab-1"></f7-link>
    </f7-nav-left>
    <div class="title">{{ title }}</div>
    <f7-nav-right>
        <f7-link tab-link="#tab-4"><f7-icon f7="bag_fill"></f7-icon><f7-badge color="pink">{{ cart_list.length }}</f7-badge></f7-link>
    </f7-nav-right>            
</f7-navbar>
<f7-subnavbar v-if="isclear">
    <f7-nav-right>
        <f7-button color="withe" @click="buttonclear()">Очистить все</f7-button>
    </f7-nav-right>
</f7-subnavbar>
<f7-appbar id="error" v-if="net_error"><p>Ошибка подключения к серверу</p></f7-appbar>    
<f7-page>
    <f7-tabs animated>
        <f7-tab id="tab-1" @tabShow="setpanel(false); settitle(menus[0].title)" class="page-content" tab-active>
            <f7-list id="item-cat" inset>
                <f7-list-button tab-link="#tab-2" @click="setcat(cat.id); settitle(cat.title)" v-for="cat in category" :style="{ backgroundImage: 'url(images/'+cat.id+'.jpg)'}">
                    <h3>{{ cat.title }}</h3>
                    <p>{{ cat.text }}</p>
                </f7-list-button>
            </f7-list>
        </f7-tab>
        <f7-tab id="tab-2" @tabShow="setpanel(true)" class="page-content">
            <div>
            <f7-card v-for="(item,index) in item_list" v-if="item.categoryId == id_cat" class="card-img">
                <f7-card-header class="no-border" valign="bottom" :style="{ backgroundImage: 'url('+item.picture+')' }">
                    <h3 class="card-name">{{ item.name }}</h3>
                    <f7-link class="item-heart" :class="{ 'active': item.heart }" @click="likeitem(item.id); item.heart = !item.heart"><f7-icon f7="heart_fill"></f7-icon></f7-link>
                </f7-card-header>
                <f7-card-content>
                    <p v-if="item.description" class="card-dir"><span>Состав:</span> {{ item.description }}</p>
                    <f7-row>
                        <f7-col>
                            <p v-if="item.weight" class="card-weight"><span>Вес:</span> {{ item.weight * 1000 }} грамм</p>
                        </f7-col>
                        <f7-col>
                            <div v-if="item.price" class="card-price">
                                <p>{{ item.price }} <span>руб.</span></p>
                            </div>
                        </f7-col>
                    </f7-row>
                </f7-card-content>
                <f7-card-footer>
                    <f7-stepper :id="'itm_'+item.id" :value="1" :min="1" :max="99" :step="1" :autorepeat="true" fill color="default"></f7-stepper>
                    <f7-button fill @click="addcart(index,'itm_')"><f7-icon f7="bag_fill_badge_plus"></f7-icon>&nbsp; В корзину</f7-button>
                </f7-card-footer>
            </f7-card>
            </div>
        </f7-tab>
        <f7-tab id="tab-3" @tabShow="setpanel(false); settitle(menus[1].title)" class="page-content">
            <div>
            <f7-block v-if="!isheart" class="error-box">
                <p>Список избранного пуст</p>
            </f7-block>
            <f7-card v-for="(item,index) in item_list" v-if="item.heart" class="card-img">
                <f7-card-header class="no-border" valign="bottom" :style="{ backgroundImage: 'url('+item.picture+')' }">
                    <h3 class="card-name">{{ item.name }}</h3>
                    <f7-link class="item-heart" :class="{ 'active': item.heart }" @click="likeitem(item.id); item.heart = !item.heart"><f7-icon f7="heart_fill"></f7-icon></f7-link>
                </f7-card-header>
                <f7-card-content>
                    <p v-if="item.description" class="card-dir"><span>Состав:</span> {{ item.description }}</p>
                    <f7-row>
                        <f7-col>
                            <p v-if="item.weight" class="card-weight"><span>Вес:</span> {{ item.weight * 1000 }} грамм</p>
                        </f7-col>
                        <f7-col>
                            <div v-if="item.price" class="card-price">
                                <p>{{ item.price }} <span>руб.</span></p>
                            </div>
                        </f7-col>
                    </f7-row>
                </f7-card-content>
                <f7-card-footer>
                    <f7-stepper :id="'her_'+item.id" :value="1" :min="1" :max="99" :step="1" :autorepeat="true" fill color="default"></f7-stepper>
                    <f7-button fill @click="addcart(index,'her_')"><f7-icon f7="bag_fill_badge_plus"></f7-icon>&nbsp; В корзину</f7-button>
                </f7-card-footer>
            </f7-card>
            </div>
        </f7-tab>
        <f7-tab id="tab-4" @tabShow="setpanel(false); settitle(menus[2].title); istab = true"  @tabHide="istab = false" class="page-content">
            <f7-list class="virtual-list">
                <f7-block v-if="!iscart" class="error-box">
                    <p>Ваша корзина пуста</p>
                </f7-block>
                
                <f7-card v-for="(item,index) in cart_list">
                    <f7-card-content>
                        <f7-row>
                            <f7-col width="25">
                                <div class="card-img" :style="{ backgroundImage: 'url('+item.picture+')' }"></div>
                            </f7-col>
                            <f7-col width="75">
                                <h3 class="cart-name">{{ item.name }}</h3>
                                <f7-row>
                                    <f7-col>
                                        <p class="t-small"><span>Вес:</span>&nbsp; {{ item.weight*1000 }} грамм</p>
                                    </f7-col>
                                    <f7-col>
                                        <p class="t-small"><span>Цена:</span>&nbsp; {{ item.price }} руб</p>
                                    </f7-col>
                                </f7-row>
                            </f7-col>
                        </f7-row>
                        <f7-row>
                            <f7-col class="f-top">
                                <f7-stepper @change="multicart(index)" :id="'cart_'+item.id" :value="item.count" :max="99" :step="1" :autorepeat="false" fill color="default"></f7-stepper>
                            </f7-col>
                            <f7-col>
                                <p class="price-total"><span>Итого:</span>&nbsp; {{ item.price * item.count }} руб.</p>
                            </f7-col>
                        </f7-row>
                    </f7-card-content>
                </f7-card>
                <f7-block v-if="iscart">
                    <f7-list simple-list>
                        <f7-list-item>
                            <f7-nav-left>
                                <p><b>Доставка:</b></p>
                            </f7-nav-left>
                            <f7-nav-right>
                                <f7-toggle :checked="delivery || isdelivery" @change="delivery = !delivery; iscarttotal()"></f7-toggle>
                            </f7-nav-right>
                        </f7-list-item>
                        <f7-list-item v-if="delivery">
                            <f7-nav-left>
                                <p>Доставка:</p>
                            </f7-nav-left>
                            <f7-nav-right>
                                <p v-if="delivery&&!isdelivery"><i>+150 руб.</i></p>
                                <p v-if="isdelivery"><i>Бесплатно</i></p>
                            </f7-nav-right>
                        </f7-list-item>
                        <f7-list-item v-if="delivery&&!isdelivery">
                            <p class="smail-text danger">Бесплатно при заказе от 500 рублей</p>
                        </f7-list-item>
                        <f7-list-item v-if="!delivery">
                            <f7-nav-left>
                                <p>Самовывоз:</p>
                            </f7-nav-left>
                            <f7-nav-right>
                                <p><i>Бесплатно</i></p>
                            </f7-nav-right>
                        </f7-list-item>
                        <f7-list-item>
                            <f7-nav-left>
                                <p><b>К оплате:</b></p>
                            </f7-nav-left>
                            <f7-nav-right v-if="isdelivery || !delivery">
                                <p><b><i>{{ cart_total }} руб.</i></b></p>
                            </f7-nav-right>
                            <f7-nav-right v-if="delivery&&!isdelivery">
                                <p><b><i>{{ cart_total+150 }} руб.</i></b></p>
                            </f7-nav-right>
                        </f7-list-item>
                    </f7-list>
                </f7-block>
                <f7-block v-if="iscart">
                    <f7-button large fill round popup-open="#place_order">Оформить заказ</f7-button>
                </f7-block>
            </f7-list>
        </f7-tab>
        <f7-tab id="tab-5" @tabShow="setpanel(false); settitle(menus[3].title)" class="page-content">
            <f7-block v-if="!ishistory" class="error-box">
                <p>Вы еще ничего не заказывали</p>
            </f7-block>
            <f7-list v-if="ishistory" accordion-list>
                <f7-list-item v-for="list in history_list" accordion-item :title="'Заказ от '+list.data">
                    <f7-accordion-content>
                        <f7-block>
                            <f7-row v-for="item in list.items" no-gap>
                                <f7-col width="50" style="text-align: left;"><b>{{item.name}}</b></f7-col>
                                <f7-col width="25" style="text-align: right;"><i>{{item.price}}</i><i>X</i><i>{{item.count}}</i></f7-col>
                                <f7-col width="25" style="text-align: right;"> = <span>{{item.price * item.count}}</span> руб.</f7-col>
                            </f7-row>
                            <f7-row>
                                <f7-col style="text-align: left;"><b>Доставка:</b></f7-col>
                                <f7-col style="text-align: right;"><i>{{list.delivery}}</i> <i v-if="list.delivery === '+150'">руб.</i></f7-col>
                            </f7-row>
                            <f7-row v-if="list.delivery === 'Бесплатно' || list.delivery === '+150'">
                                <f7-col style="text-align: left;"><b>Адрес доставки:</b></f7-col>
                                <f7-col style="text-align: right;"><i>{{list.addr}}</i></f7-col>
                            </f7-row>
                            <f7-row v-if="list.delivery === 'Бесплатно' || list.delivery === 'Самовывоз'" style="text-align: right;">
                                <f7-col><b>Итого:</b> {{list.total}} руб.</f7-col>
                            </f7-row>
                            <f7-row v-if="list.delivery === '+150'" style="text-align: right;">
                                <f7-col><b>Итого:</b> {{list.total+150}} руб.</f7-col>
                            </f7-row>
                        </f7-block>
                    </f7-accordion-content>
                </f7-list-item>
            </f7-list>
        </f7-tab>
        <f7-tab id="tab-6" @tabShow="setpanel(false); settitle(menus[4].title)" class="page-content">
            <f7-block id=onas>
                <h2><b>О компании</b></h2>
                <p>«ОК Суши» уже не мало месяцев завоевывает сердца жителей Абакана, Черногорска и близлежайших населенных пунктов. И хотя сегодня существует немало мест, где вы можете полакомиться суши и роллами, далеко не все заведения могут похвастаться бесплатной доставкой при антикризисных ценах и завидном ассортименте.</p>
                <p>«ОК Суши» предлагает вам лучшие блюда японской кухни по самым привлекательным ценам. В качестве бонуса к разнообразным сетам, роллам, суши прилагаются бесплатная доставка и различные подарки и акции. Мы дарим вам возможность пообедать или поужинать в привычной домашней обстановке или в офисе.</p>
                <p>Мы по праву гордимся тем, что в заботе о здоровье своих клиентов, которым полюбилась японская кухня в нашем исполнении, готовим свою продукцию только из высококачественного сырья от ведущих поставщиков ингредиентов для японских блюд. Наши суши – это вкусно, быстро и безопасно.</p>
                <p>Став постоянными клиентами «ОК Суши», вы узнаете, что такое высококлассная японская кухня без ресторанных наценок. Бесплатная доставка заказов по центру города от 500 рублей ( в отдельные районы доставка может стоить отдельных денег), осуществляемая курьерами, позволит вам не остаться голодным в любое время <b>с 11 до 23 часов.</b> (Если заказываете по акции или со скидкой доставка всегда является платной).</p>
                <p>В случае если у Вас возникли вопросы при оформлении заказа, Вы всегда можете обратиться в наш справочный центр по телефону <f7-link href="tel:+73902313430" external>+7(3902)313-430</f7-link>.</p>
            </f7-block>
        </f7-tab>
    </f7-tabs>
</f7-page>
  <!-- Popup -->
  <f7-popup id="place_order">
    <f7-view>
      <f7-page>
        <f7-navbar title="Оформление заказа">
          <f7-nav-right>
            <f7-link popup-close><f7-icon f7="xmark"></f7-icon></f7-link>
          </f7-nav-right>
        </f7-navbar>
            <f7-list no-hairlines-md>
                <f7-list-input 
                    label="Ваше имя"
                    type="text"
                    placeholder="Имя"
                    :value="user.name"
                    @input="user.name = $event.target.value"
                    required validate
                    clear-button>
                </f7-list-input>
                <f7-list-input 
                    label="Телефонный номер"
                    type="tel"
                    placeholder="Телефон"
                    :value="user.phone"
                    @input="user.phone = $event.target.value"
                    required validate
                    info="Номер телефона в междунарожном формате"
                    error-message="Не корректный телефонный номер"
                    pattern="^((\+7|7|8)+([0-9]){10})$"
                    clear-button>
                </f7-list-input>
                <f7-list-input v-if="delivery"
                    label="Адрес доставки"
                    type="text"
                    placeholder="Адрес"
                    :value="user.addr"
                    @input="user.addr = $event.target.value"
                    required validate
                    clear-button>
                </f7-list-input>
                <f7-list-input
                    label="Количество персон"
                    type="number"
                    placeholder="Персон"
                    :value="user.per"
                    @input="user.per = $event.target.value"
                    validate
                    info="Необходимое количество приборов"
                    error-message="Только цыфроми"
                    pattern="[0-9]*"
                    clear-button>
                </f7-list-input>
                <f7-list-input 
                    label="Комментарий к заказу"
                    type="textarea"
                    :value="user.comment"
                    @input="user.comment = $event.target.value"
                    clear-button>
                </f7-list-input>
            </f7-list>
            <f7-block>
                <f7-button large fill round :disabled="!user.name || !user.phone || !isphone || delivery&&!user.addr" @click="senddata()">Оформить</f7-button>
            </f7-block>
      </f7-page>
    </f7-view>
  </f7-popup>
</f7-app>
</template>

<script>
  import { Device }  from 'framework7/framework7-lite.esm.bundle.js';
  import cordovaApp from '../js/cordova-app.js';
  import axios from 'axios';

  import '../assets/images/589552641.jpg';
  import '../assets/images/589550241.jpg';
  import '../assets/images/623330441.jpg';
  import '../assets/images/589550641.jpg';
  import '../assets/images/589551041.jpg';
  import '../assets/images/38186416.jpg';
  import '../assets/images/432653016.jpg';
  import '../assets/images/559725816.jpg';
  import '../assets/images/no-foto.png';
  import '../assets/images/fonscreen.jpg';
  export default {
      data() {
          return {
                // Framework7 Parameters
                f7params: {
                    id: 'ru.mrdogos.oksushi19', // App bundle ID
                    name: 'oksushi19', // App name
                    theme: 'auto', // Automatic theme detection
                    // Input settings
                    input: {
                        scrollIntoViewOnFocus: Device.cordova && !Device.electron,
                        scrollIntoViewCentered: Device.cordova && !Device.electron,
                    },
                    // Cordova Statusbar settings
                    statusbar: {
                        iosOverlaysWebView: true,
                        androidOverlaysWebView: false,
                    },
                },
                // Datas
                user: {
                    uuid: '',
                    name: '',
                    phone: '',
                    addr: '',
                    per: 1,
                    comment: '',
                },
                title: 'Категории',
                active_arrow: false,
                id_cat: null,
                net_error: false,
                istab: false,
                cart_total: 0,
                delivery: true,
                history_list:[],
                item_list: [],
                heart_list: [],
                cart_cat: [],
                cart_list:[],
                category: [
                    {
                        id: '589552641',
                        title: 'Суши Сеты',
                        text: 'Суши сеты - это набор роллов по заранее сниженной цене от 5% - 15%.'
                    },
                    {
                        id: '589550241',
                        title: 'Роллы',
                        text: 'Доставим до квартиры или офиса вкуснейшие роллы в течении часа.'
                    },
                    {
                        id: '623330441',
                        title: 'Тортильи',
                        text: 'Это ролл приготовленный в лепешке из кукурузной муки с разнообразной начинкой.'
                    },
                    {
                        id: '589550641',
                        title: 'Маки',
                        text: 'Маки - это ролл обвернутый полностью листом нори с разнообразной начинкой.'
                    },
                    {
                        id: '589551041',
                        title: 'Горячие роллы',
                        text: 'Доставим до квартиры или офиса вкуснейшие роллы в течении часа.'
                    },
                    {
                        id: '38186416',
                        title: 'Фри',
                        text: 'Доставим до квартиры или офиса крылышки - картофель в течении часа.'
                    },
                    {
                        id: '432653016',
                        title: 'Пицца',
                        text: 'Бесплатная доставка вкусной пиццы в Абакане на дом и в офис.'
                    },
                    {
                        id: '559725816',
                        title: 'Дополнительно',
                        text: 'Здесь вы найдете роллы которых нет в меню, но они есть в наборах.'
                    }
                ],
                menus: [
                    {
                        icon: 'rectangle_on_rectangle_angled',
                        title: 'Категории',
                        href: '#tab-1',
                        badgeheart: false
                    },
                    {
                        icon: 'heart',
                        title: 'Избранное',
                        href: '#tab-3',
                        badgeheart: true
                    },
                    {
                        icon: 'cart',
                        title: 'Корзина',
                        href: '#tab-4',
                        badgecart: true
                    },
                    {
                        icon: 'doc_text',
                        title: 'История заказов',
                        href: '#tab-5',
                        badgeheart: false
                    },
                    {
                        icon: 'info_circle',
                        title: 'О компании',
                        href: '#tab-6',
                        badgeheart: false
                    }
                ],
          }
      },
      mounted() {
          this.$f7ready((f7) => {
              // Init cordova APIs (see cordova-app.js)
              if (Device.cordova) {
                  cordovaApp.init(f7);
              }
              this.user.uuid = this.$refs.uuid.getAttribute('data-id');
              // Call F7 APIs here
              this.loadxml();
          });
      },
      computed: {
          isheart: function(){
              return(this.heart_list.length > 0 ? true : false);
          },
          iscart: function(){
              return(this.cart_list.length > 0 ? true : false);
          },
          isdelivery: function(){
              return((this.delivery) && this.cart_total > 500 ? true : false);
          },
          isphone: function(){
              return (/^((\+7|7|8)+([0-9]){10})$/.test(this.user.phone)) ? true : false;
          },
          isclear: function(){
              return((this.istab) && this.cart_list.length > 0 ? true : false);
          },
          ishistory: function(){
              return(this.history_list.length > 0 ? true : false);
          }
      },
      methods: {
          save_heart: function(){
              axios.get('https://webmaster19.ru/app/index.php?heart_id='+this.user.uuid+'&data='+this.heart_list.join()).catch(error => {this.net_error = true});
          },
          save_cart: function(){
              axios.get('https://webmaster19.ru/app/index.php?cart_id='+this.user.uuid+'&data='+this.cart_cat.join()).catch(error => {this.net_error = true});
          },
          save_hstory: function(order){
              var dely = (this.delivery) ? 'true' : 'false';
              axios.get('https://webmaster19.ru/app/index.php?history=true&data_user='+JSON.stringify(this.user)+'&data_order='+JSON.stringify(order)+'&delivery='+dely).then((response) => {
                  this.history_list = JSON.parse(JSON.stringify(response.data));
                  this.history_list.sort((a, b) => (a > b ? 1 : -1));
                  this.$f7.preloader.hide();
                  this.$f7.dialog.alert('Спасибо!<br>Ваша заявка принята!<br>Мы свяжемся свами в ближайшее время по тел:'+this.user.phone,'Сообщение',()=>{
                      this.user.name = '';this.user.phone = '';this.user.addr = '';this.user.per = 1;this.user.comment = '';
                      this.$f7.popup.close('#place_order',true);
                      this.clearcart();
                      this.$f7.tab.show('#tab-1', true);
                  });
              }).catch(error => {this.net_error = true});
          },
          loadxml: function(){
              var tmp_list = [];
              axios.get('https://webmaster19.ru/app/index.php?app_id='+this.user.uuid).then((response) => {
                  if(response.data){
                      var tmp_data = response.data.xml.shop.offers.offer;
                      var tmp_cart = [];
                      var temp_heart = (response.data.heart.length > 0) ? response.data.heart.split(',') : [];
                      var temp_cart = (response.data.cart.length > 0) ? response.data.cart.split(',') : [];
                      this.history_list = JSON.parse(JSON.stringify(response.data.history));
                      this.history_list.sort((a, b) => (a > b ? 1 : -1));
                      tmp_data.forEach(function(entry){
                      var item = {
                          id: entry['@attributes'].id,
                          categoryId: entry.categoryId,
                          picture: entry.picture = entry.picture ? entry.picture : 'images/no-foto.png',
                          name: entry.name,
                          description: entry.description,
                          price: entry.price,
                          weight: entry.weight,
                          delivery: entry.delivery,
                      }
                      let index = temp_heart.indexOf(item.id);
                          item['heart'] = entry.heart = index > -1 ? true : false;
                      index = temp_cart.indexOf(item.id);
                          if(index > -1){
                             item['count'] = +temp_cart[index+1];
                             tmp_cart.push(item);
                          }
                          tmp_list.push(item);
                      });
                      this.item_list = JSON.parse(JSON.stringify(tmp_list));
                      this.heart_list = JSON.parse(JSON.stringify(temp_heart));
                      this.cart_list = JSON.parse(JSON.stringify(tmp_cart));
                      this.iscarttotal();
                  }else{
                      this.net_error = true;
                  }
              }).catch(error => {this.net_error = true});
          },
          settitle: function(title){
              this.title = title;
          },
          setcat: function(id){
              this.id_cat = id;
          },
          setpanel: function(arrow){
              this.$f7.panel.close('.panel-left', true);
              this.active_arrow = arrow;
          },
          likeitem: function(id){
              let index = this.heart_list.indexOf(id);
              if(index > -1){
                  this.heart_list.splice(index, 1);
              }else{
                  this.heart_list.push(id);
              }
              this.save_heart();
          },
          iscarttotal: function(){
              var total = 0;
              var tmp_cart = [];
              this.cart_list.forEach(function(entry){
                  total = total + entry.price * entry.count;
                  tmp_cart.push(entry.id);
                  tmp_cart.push(entry.count);
              });
              this.cart_total = JSON.parse(JSON.stringify(total));
              this.cart_cat = JSON.parse(JSON.stringify(tmp_cart));
              this.save_cart();
          },
          addcart: function(index,pref){
              var item = this.item_list[index];
              var count = this.$f7.stepper.getValue('#'+pref+item.id);
              var flag = false;
              if(this.cart_list.length > 0){
              for(var i = 0; i < this.cart_list.length; i++){
                  if(this.cart_list[i].id == item.id){
                      this.cart_list[i].count = this.cart_list[i].count + count;
                      flag = true;
                      break;
                  }
              }};
              if(!flag){
                  item['count'] = +count;
                  this.cart_list.push(item);                  
              };
              this.iscarttotal();
          },
          multicart: function(index){
              var tmp_cart = this.cart_list;
              var item = tmp_cart[index];
              var count = this.$f7.stepper.getValue('#cart_'+item.id);
              if(count <= 0){
                  tmp_cart.splice(index, 1);
              }else{
                  tmp_cart[index].count = +count;
              }
              this.cart_list = JSON.parse(JSON.stringify(tmp_cart));
              this.iscarttotal();
          },
          clearcart: function(){
                  var tmp_cart = [];
                  this.cart_list = JSON.parse(JSON.stringify(tmp_cart));
                  this.iscarttotal();
          },
          buttonclear: function(){
              this.$f7.dialog.confirm('<p style="text-align: center;">Очистить корзину ?</p>','',()=>{
                  this.clearcart();
              });              
          },
          senddata: function(){
              var tmp_data = [];
              this.cart_list.forEach(function(entry){
                  var item = {
                      name: entry.name,
                      cont: entry.count,
                      price: entry.price,
                  }
                  tmp_data.push(item);
              });
              this.$f7.preloader.show();
              this.save_hstory(tmp_data);
          },
          exitapp: function(){
              this.$f7.dialog.confirm('<p style="text-align: center;">Закрыть приложение ?</p>','',()=>{
                 navigator.app.exitApp();
              });
          }          
      }
  }
</script>