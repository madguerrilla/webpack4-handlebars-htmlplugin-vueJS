// import Vue from 'Vue';
import axios from 'axios';

const app = new Vue({
    delimiters: ['{(', ')}'],
    el: '#app',
    created() {
        this.fetchData();
    },
    data: {
        profiles: [],
        errors: [],
        fetch: 12,
        from: 1,
        to: 12,
        loading: false,
        loadMore: true,
        loadButton: 'Load more profiles...',
        complete: false,
        firstRendered: ''
    },
    methods: {
        fetchData() {
            this.loading = true;
            this.loadMore = false;
            axios.get('http://localhost:8081/api/from/' + this.from + '/to/' + this.to)
                .then(response => {
                    response.data.result.forEach((_this) => {
                        this.profiles.push(_this);
                    });
                    this.from = this.from + this.fetch;
                    this.to = this.to + this.fetch;
                    this.loading = false;
                    this.loadMore = true;
                    this.firstRendered = response.data.firstRendered;
                    if (response.data.complete) {
                        this.complete = true;
                        this.loadButton = 'No more profiles...';
                    }
                    console.log(response);
                })
                .catch(e => {
                    this.errors.push(e);
                    this.loading = false;
                    this.loadMore = true;
                });
                $('html, body').animate({
                    scrollTop: $('#profile-' + String(this.firstRendered)).offset()
                }, 2000);
        }
    },
    computed: {
        isDisabled: function() {
            return this.complete;
        }
    }
});

Vue.component('profile-component', {
    template: `
      <div class="col-md-4 col-sm-6">
          <div v-bind:id="'profile-' + profile._id" class="profile" v-bind:class="{ 'profile--active': profile.isActive }">
              <div class="profile__image">
                  <img v-bind:src="profile.picture" v-bind:alt="profile.name">
              </div>
              <div class="profile__details">
                  <ul class="profile__details-list">
                      <li class="profile__details-list-item">{{profile.name}}, {{profile.age}}</li>
                      <li class="profile__details-list-item"><a v-bind:href="profile.phone">{{profile.phone}}</a></li>
                      <li class="profile__details-list-item">{{profile.email}}</li>
                  </ul>
              </div>
          </div>
      </div>`,
    props: {
        profile: Object
    }
});
