<template>
  <div id="app" class="flex container h-screen w-full">
    <!-- side nav -->
    <div class="lg:w-1/5 border-r border-lighter px-2 lg:px-6 py-2 flex flex-col justify-between">
      <div>
        <button class="h-12 w-12 hover:bg-lightblue text-3xl rounded-full text-blue">
          <i class="fab fa-twitter"></i>
        </button>
        <div>
          <button v-for="tab in tabs" :key="tab.title" @click="id = tab.id" :class="`focus:outline-none hover:text-black flex items-center py-2 px-4 hover:bg-gray-300 rounded-full mr-auto mb-3 ${ id === tab.id ? 'text-black font-bold' : ''}`">
            <i :class="`${ tab.icon } text-2xl mr-4 text-left`"></i>
            <p class="text-lg font-extralight text-left hidden lg:block"> {{ tab.title }} </p>
          </button>
        </div>
        <button class="text-white bg-blue rounded-full font-semibold focus:outline-none w-12 h-12 lg:h-auto lg:w-full p-3 hover:bg-darkblue" @click="$modal.show('modal-tweet')">
          <p class="hidden lg:block">Tweet</p>
          <i class="fas fa-plus lg:hidden"></i>
        </button>
      </div>
      <div class="lg:w-full relative">
        <button @click="dropdown = true" class="flex items-center w-full hover:bg-lightblue rounded-full p-2 focus:outline-none">
          <img src="profile.png" class="w-10 h-10 rounded-full border border-lighter" />
          <div class="hidden lg:block ml-4">
            <p class="text-sm font-bold leading-tight"> Reza Mandala </p>
            <p class="text-sm leading-tight"> @rezamandala </p>
          </div>
          <i class="hidden lg:block fas fa-ellipsis-h ml-auto text-lg"></i>
        </button>
        <div v-if="dropdown === true" class="absolute bottom-0 left-0 w-64 rounded-lg shadow-md border-lightest bg-white mb-16">
          <button @click="dropdown = false" class="p-3 flex items-center w-full hover:bg-lightest p-2 focus:outline-none">
            <img src="profile.png" class="w-10 h-10 rounded-full border border-lighter" />
            <div class="ml-4">
              <p class="text-sm font-bold leading-tight"> Reza Mandala </p>
              <p class="text-sm leading-tight"> @rezamandala </p>
            </div>
            <i class="fas fa-check ml-auto text-blue"></i>
          </button>
          <button @click="dropdown = false" class="w-full text-left hover:bg-lightest border-t border-lighter p-3 text-sm focus:outline-none">
            Tambahkan akun yang sudah ada
          </button>
          <button @click="dropdown = false" class="w-full text-left hover:bg-lightest border-t border-lighter p-3 text-sm focus:outline-none">
            Keluar dari @rezamandala
          </button>
        </div>
      </div>
    </div>
    <!-- tweets -->
    <div class="w-full md:w-1/2 h-full overflow-y-scroll no-scrollbar">
      <div class="px-5 py-3 border-lighter flex items-center justify-between">
        <h1 class="text-xl font-bold">Beranda</h1>
        <i class="far fa-star text-xl text-blue"></i>
      </div>
      <div class="px-5 py-3 border-b border-lighter flex">
        <div class="flex-none">
          <img src="profile.png" class="flex-none w-12 h-12 rounded-full border border-lighter"/>
        </div>
        <form v-on:submit.prevent = "addNewTweet" class="w-full px-4 relative">
          <textarea v-model="tweet.content" placeholder="Apa yang sedang terjadi?" class="mt-3 pb-3 w-full focus:outline-none"/>
          <div class="flex items-center">
            <i class="text-lg text-blue mr-4 far fa-image"></i>
            <i class="text-lg text-blue mr-4 far fa-chart-bar"></i>
            <i class="text-lg text-blue mr-4 far fa-smile"></i>
            <i class="text-lg text-blue mr-4 far fa-calendar"></i>
          </div>
          <button type="submit" class="h-10 px-4 text-white font-semibold bg-blue hover:bg-darkblue focus:outline-none rounded-full absolute bottom-0 right-0">
            Tweet
          </button>
        </form>
      </div>
      <div class="flex flex-col-reverse">
        <div v-for="tweet in tweets" :key="tweet.content" class="w-full p-4 border-b hover:bg-lighter flex">
          <div class="flex-none mr-4">
            <img src="profile.png" class="h-12 w-12 rounded-full flex-none"/>
          </div>
          <div class="w-full">
            <div class="flex items-center w-full">
              <p class="font-semibold"> Reza Mandala </p>
              <p class="text-sm text-dark ml-2"> @rezamandala </p>
              <p class="text-sm text-dark ml-2"> · {{ tweet.time }} </p>
              <i class="fas fa-ellipsis-h text-dark ml-auto"></i>
            </div>
            <p class="py-2">
              {{ tweet.content }}
            </p>
            <div class="flex items-center justify-between w-full">
              <div class="flex items-center text-sm text-dark">
                <i class="far fa-comment mr-3"></i>
                <p> 0 </p>
              </div>
              <div class="flex items-center text-sm text-dark">
                <i class="fas fa-retweet mr-3"></i>
                <p> 0 </p>
              </div>
              <div class="flex items-center text-sm text-dark">
                <i class="fas fa-heart mr-3"></i>
                <p> 1 </p>
              </div>
              <div class="flex items-center text-sm text-dark">
                <i class="fas fa-share mr-3"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-for="follow in following" :key="follow.name" class="w-full p-4 border-b hover:bg-lighter flex">
        <div class="flex-none mr-4">
          <img :src="`${follow.src}`" class="h-12 w-12 rounded-full flex-none"/>
        </div>
        <div class="w-full">
          <div class="flex items-center w-full">
            <p class="font-semibold"> {{ follow.name }} </p>
            
            <i :class="`fas ${ follow.verif === true ? 'fa-check-circle' : ''} ml-1 text-blue `"></i>
            <p class="text-sm text-dark ml-2"> {{ follow.handle }} </p>
            <p class="text-sm text-dark ml-2"> · {{ follow.time }} </p>
            <i class="fas fa-ellipsis-h text-dark ml-auto"></i>
          </div>
          <p class="py-2">
            {{ follow.tweet }}
          </p>
          <div class="flex items-center justify-between w-full">
            <div class="flex items-center text-sm text-dark">
              <i class="far fa-comment mr-3"></i>
              <p> {{ follow.comments }} </p>
            </div>
            <div class="flex items-center text-sm text-dark">
              <i class="fas fa-retweet mr-3"></i>
              <p> {{ follow.retweets }} </p>
            </div>
            <div class="flex items-center text-sm text-dark">
              <i class="fas fa-heart mr-3"></i>
              <p> {{ follow.like }} </p>
            </div>
            <div class="flex items-center text-sm text-dark">
              <i class="fas fa-share mr-3"></i>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- trending -->
    <div class="md:block hidden w-1/3 h-full border-l border-lighter py-2 px-6 overflow-y-scroll relative no-scrollbar">
      <input class="pl-12 rounded-full w-full p-2 bg-lighter text-sm mb-4" placeholder="Cari di Twitter" />
      <i class="fas fa-search absolute left-0 top-0 mt-5 ml-12 text-sm text-light"></i>
      <div class="w-full rounded-lg bg-lightest">
        <div class="flex items-center justify-between p-3">
          <p class="text-lg font-bold">Tren Indonesia</p>
        </div>
        <button v-for="trend in trending" :key="trend.title" class="w-full flex justify-between hover:bg-lighter p-3 border-t border-lighter">
          <div>
            <p class="text-xs text-left leading-tight text-dark"> {{ trend.top}} </p>
            <p class="font-semibold text-sm text-left leading-tight"> {{ trend.title}} </p>
            <p class="text-left text-sm leading-tight text-dark"> {{ trend.bottom}} </p>
          </div>
          <i class="fas fa-ellipsis-h text-lg text-dark"></i>
        </button>
        <button class="p-3 w-full hover:bg-lighter text-left text-blue border-t border-lighter">
          Tampilkan lebih banyak
        </button>
      </div>
      <div class="w-full rounded-lg bg-lightest my-4">
        <div class=" p-3">
          <p class="text-lg font-bold">Untuk diikuti</p>
        </div>
        <button v-for="friend in friends" :key="friend.name" class="w-full flex hover:bg-lighter p-3 border-t border-lighter">
          <img :src="`${ friend.src }`" class="w-12 h-12 rounded-full border border-lighter" />
          <div class="hidden lg:block ml-4">
            <p class="text-sm font-bold leading-tight"> {{ friend.name }} </p>
            <p class="text-sm leading-tight"> {{ friend.handle }} </p>
          </div>
          <button class="ml-auto text-sm text-white py-1 px-4 rounded-full bg-black">
            Follow
          </button>
        </button>
        <button class="p-3 w-full hover:bg-lighter text-left text-blue border-t border-lighter">
          Tampilkan lebih banyak
        </button>
      </div>
    </div>
    
  </div>
</template>

<script>

export default {
  name: 'app',
  components: {
  },
  data() {
    return {
      tabs: [
        {icon: 'fas fa-home', title: 'Beranda', id:'home'},
        {icon: 'fas fa-hashtag', title: 'Jelajahi', id: 'explore'},
        {icon: 'fas fa-user-friends', title: 'Komunitas', id: 'community'},
        {icon: 'far fa-bell', title: 'Notifikasi', id: 'notifications'},
        {icon: 'far fa-envelope', title: 'Pesan', id: 'messages'},
        {icon: 'far fa-user', title: 'Profil', id: 'profile'},
        {icon: 'fas fa-ellipsis-h', title: 'Lainnya', id: 'more'}
      ],
      id: 'home',
      dropdown: false,
      trending: [
        {top: '1 · Sepakbola · Populer', title: '#TIMNASDAY', bottom: '1.7M Tweet'},
        {top: '2 · K-pop · Populer', title: 'BTS', bottom: '123k Tweet'},
        {top: '3 · Sedang Tren', title: 'Egy Maulana Vikri', bottom: '230k Tweet'},
        {top: '4 · Film & TV · Populer', title: 'KKN Desa Penari', bottom: '666k Tweet'},
        {top: '5 · Sedang Tren', title: 'Thomas Cup', bottom: '500k Tweet'},
        {top: '5 · Music · Sedang Tren', title: 'Happy Asmara', bottom: '67k Tweet'},
        {top: '7 · Sedang Tren', title: 'Dr Strange', bottom: '88.3k Tweet'},
      ],
      friends: [
        {src: 'raditya.jpg', name: 'raditya dika', handle: '@radityadika'},
        {src: 'corbuzier.jpg', name: 'Corbuzier', handle: '@corbuzier'},
        {src: 'armuh.jpg', name: 'Pocong', handle: '@poconggg'}
      ],
      following: [
        {src: 'najwa.jpg', name: 'Najwa Shihab', handle: '@NajwaShihab', time: '25mnt', verif: true, tweet: 'Saatnya yang muda yang berperan dengan kreativitas yang tak gampang padam.', comments: '445', retweets: '5.124', like: '2.000'},
        {src: 'detikcom.jpg', name: 'detikcom', handle: '@detikcom', time: '1j', verif: true, tweet: 'Kondisi terkini Tol Cipali yang mengarah ke timur atau Jawa Tengah dan seterusnya masih terpantau padat walaupun sudah diberlakukan contra flow.', comments: '10', retweets: '50', like: '67'},
        {src: 'shin.jpg', name: 'Shin Tae Yong', handle: '@shintaeyong', time: '4j', verif: false, tweet: 'Annyeonghaseyo', comments: '230.080', retweets: '2.457.000', like: '10.676.000'},
        {src: 'motogp.jpg', name: 'MotoGP™🏁', handle: '@MotoGP', time: '12j', verif: true, tweet: 'New circuits provide new challenges, and the fastest learners will earn the ultimate reward! #IndonesianGP', comments: '26.000', retweets: '502.000', like: '132.990'},
        {src: 'willy.jpg', name: 'willy', handle: '@willythekid', time: '1hr', verif: false, tweet: 'siapa mau ikut nonton KKN Desa Penari', comments: '569', retweets: '980', like: '1.908'},
        {src: 'jek.jpg', name: 'jek', handle: '@jek', time: '2hr', verif: false, tweet: 'alhamdulillah dapet vespa gratis', comments: '45', retweets: '1.245', like: '2.354'}
      ],
      tweets: [
        {content: 'Hello World!', time: '10mnt'}
      ],
      tweet: {content: '', time: ''}
    }
  },
  methods: {
    addNewTweet () {
      const today = new Date();
      const date = today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate();
      const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
      const dateTime = date +' '+ time;

      let newTweet = {
        content: this.tweet.content,
        time: dateTime
      };

      if (this.tweet.content != "") {
        this.tweets.push (newTweet)
      }
      
      this.tweet.content = "";
    },
    modalTweet() {
      let element = this.$refs.modal
      $(element).modal('show')
    },

  }
}
</script>

<style>
.no-scrollbar::-webkit-scrollbar {
    display: none;
}

.no-scrollbar {
    -ms-overflow-style: none;  /* IE and Edge */
    scrollbar-width: none;  /* Firefox */
}
</style>
