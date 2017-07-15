<template>
  <div class="hello" id="container">
    <div class="row center text-center">
      <div class="card-heading">
        <h1>Pomodoro Clock</h1>
      </div>
    </div>
    <div class="row">
      <div class="col s12 m6 push-m3">
        <div class="card">
          <div class="card-content black-text" id="card-content">
            <span class="card-title center">Set Time</span>
            <!-- <button class="btn-floating waves-effect waves-light"><i class="material-icons">add</i></button> -->
            <div class="row">
              <div class="col s4 m4 hours">
                <div class="digits">{{hours}}</div>
                <div class="actions">
                  <button @click="decCounter('hours')" id="hours" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">remove</i>
                  </button>
                  <button @click="incCounter('hours')" id="hours" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">add</i>
                  </button>
                </div>
              </div>
              <div class="col s4 m4 minutes">
                <div class="digits">{{minutes}}</div>
                <div class="actions">
                  <button @click="decCounter('minutes')" id="minutes" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">remove</i>
                  </button>
                  <button @click="incCounter('minutes')" id="minutes" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">add</i>
                  </button>
                </div>
              </div>
              <div class="col s4 m4 seconds">
                <div class="digits">{{seconds}}</div>
                <div class="actions">
                  <button @click="decCounter('seconds')" id="seconds" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">remove</i>
                  </button>
                  <button @click="incCounter('seconds')" id="seconds" class="btn-floating waves-effect waves-light">
                    <i class="material-icons">add</i>
                  </button>
                </div>
              </div>
            </div>
            <div class="row timer-action">
              <div class="col s12 m12" style="margin-bottom:10px;">
                <div class="col s4 m4 center">
                  <button @click="start" class="btn waves-light waves-effect blue accent-4 white-text">Start</button>
                </div>
                <div class="col s4 m4 center">
                  <button @click="reset" class="btn waves-light waves-effect blue accent-4 white-text">Reset</button>
                </div>
                <div class="col s4 m4 center">
                  <button @click="pause" class="btn waves-light waves-effect blue accent-4 white-text">Pause</button>
                </div>
              </div>
              <div class="col s12 m12 center">
                <button @click="start25Min" class="btn waves-light waves-effect red white-text">Start 25 Min Clock</button>
              </div>
            </div>
          </div>

          <div class="col s12 m12 card-actions">
                <a class="action-button-normal" href="#volume_off" @click="mute"><i class="material-icons">volume_off</i></a>
                <a class="action-button-normal" style="float:right;" href="#full-screen" @click="requestFullScreen">
                <i class="material-icons">settings_overscan</i></a>
          </div>
          <div class="clearfix"></div>
        </div>
      </div>
    </div>
    <!-- Modal For Notification Of Timer Expiry -->
    <div id="modal1" class="modal bottom-sheet">
      <div class="modal-content">
        <h4>Time Up!</h4>
      </div>
    </div>
    <div class="row">
      <div class="card col s12 m6 push-m3">
        <div class="card-content">
          <div class="col s12 m6 center">
            <a target="_blank" class="" href="http://twitter.com/sh4hidkh4n"><b>Created By @sh4hidkh4n</b></a>
          </div>
          <div class="col s12 m6 center">
            <a href="http://github.com/sh4hidkh4n/"><b>Source Code</b><i class="material-icons">launch</i></a>
          </div>
          <div class="clearfix"></div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data() {
    return {
      hours: '00',
      minutes: '00',
      seconds: '10',
      intervalID: 0,
      muted : false
    }
  },
  methods: {
    incCounter(c) {
      this.counterAction('add', c);
    },
    decCounter(c) {
      this.counterAction('dec', c);
    },
    counterAction(a, c) {
      let h = (a == 'add') ? parseInt(this[c]) + 1 : (parseInt(this[c]) <= 0) ? 0 : parseInt(this[c]) - 1;
      this[c] = this.padNum(h)
    },
    start(e) {
      let _self = this;
      clearInterval(this.intervalID);
      this.intervalID = setInterval(function () {
        let hr = parseInt(_self.hours);
        let mn = parseInt(_self.minutes);
        let sc = parseInt(_self.seconds);
        let total = (((hr * 60) + mn) * 60) + sc - 1;
        console.log(total);
        if(total > 0){
          _self.setFromTotal(total);
        }else{
          // stopwatch ended play sound
          _self.seconds = '00'
          clearInterval(_self.intervalID);
          _self.playSound()
        }
      }, 1000);
    },
    start25Min(e){
      this.hours = this.padNum(0);
      this.minutes = this.padNum(25);
      this.seconds = this.padNum(0);
      this.start(e);
    },
    playSound(){
      var audioElement = document.createElement('audio');
      audioElement.setAttribute('src', 'http://www.soundjay.com/misc/sounds/bell-ringing-01.mp3');
      (!this.muted) ? audioElement.play() : this.notifyVisually();
    },
    notifyVisually(){
       $('#modal1').modal('open');
    },
    padNum(n) {
      return n < 10 ? '0' + n : n;
    },
    setFromTotal(total) {
      let sc = total % 60;
      let total_mn = (total - sc) / 60;
      let mn = total_mn % 60;
      let hr = (total_mn - mn) / 60;
      this.seconds = this.padNum(sc);
      this.minutes = this.padNum(mn);
      this.hours = this.padNum(hr);
    },
    pause(e) {
      (this.intervalID != 0) ? clearInterval(this.intervalID) : '';
    },
    requestFullScreen(e){
      var isInFullScreen = (document.fullScreenElement && document.fullScreenElement !==     null) ||    // alternative standard method  
            (document.mozFullScreen || document.webkitIsFullScreen);
      // change btn color
      this.toggleActionClass($(e.target).parent());
      var docElm = document.documentElement;
      if (!isInFullScreen) {
          if (docElm.requestFullscreen) {
              docElm.requestFullscreen();
          }
          else if (docElm.mozRequestFullScreen) {
              docElm.mozRequestFullScreen();
          }
          else if (docElm.webkitRequestFullScreen) {
              docElm.webkitRequestFullScreen();
          }
      }else if(isInFullScreen){
        if (docElm.requestFullscreen) {
              document.exitFullscreen();
          }
          else if (docElm.mozRequestFullScreen) {
              document.mozCancelFullScreen();
          }
          else if (docElm.webkitRequestFullScreen) {
              document.webkitCancelFullScreen();
          }else{
            document.exitFullscreen();
          }
      }
    },
    reset(){
      this.hours = this.minutes = this.seconds = this.padNum(0);
    },
    mute(e){
      this.muted = this.muted ? false : true;
      let parent = $(e.target).parent();
      this.toggleActionClass(parent);
    },
    toggleActionClass(elem){
      let className =elem[0].className;
      console.log(className);
      (className == 'action-button-normal') ? 
            elem.attr("class", "action-button-toggeled")
          : elem.attr("class", "action-button-normal");
    }
  }
}
</script>

<style>
.timer-action{
  padding-bottom: 0px;
  margin-bottom:5px;
}

html {
    background-color: #52B2CF;
}

#card-content{
  padding-bottom: 10px;
}
.action-button-normal{
  color: grey;
}

.material-icons{
  vertical-align: middle;
}

.action-button-toggeled{
  color: #52B2CF;
}

.card-actions{
  margin-bottom: 0.7em;
}

.digits {
  font-size: 5em;
}
.hours, .minutes, .seconds {
  text-align: center;
}
</style>
