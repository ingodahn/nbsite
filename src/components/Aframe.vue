<template>
  <div>
    <iframe :src="src" :width="width" :height="height" :id="id" v-on:load="setIframeHeight()"></iframe>
  </div>
</template>

<script>
export default {
  name: "Aframe",
  props: {
    src: {
      type: String
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "800px",
    },
    id: {
      type: String,
      default: "aframe"
    }
  },
  data() {
    return {

    }
  },
  methods: {
    // usage: <iframe id="ifrm" src="pages/height1.html" onload="setIframeHeight(this.id)"></iframe>
    getDocHeight: function(doc) {
        doc = doc || document;
        // stackoverflow.com/questions/1145850/
        var body = doc.body, html = doc.documentElement;
        var height = Math.max( body.scrollHeight, body.offsetHeight,
            html.clientHeight, html.scrollHeight, html.offsetHeight );
        return height;
    },

    setIframeHeight: function() {
      if (this.sameOrigin) {
        var ifrm = document.getElementById(this.id);
        var doc = ifrm.contentDocument? ifrm.contentDocument:ifrm.contentWindow.document;
        ifrm.style.visibility = 'hidden';
        ifrm.style.height = "10px"; // reset to minimal height ...
        // IE opt. for bing/msn needs a bit added or scrollbar appears
        ifrm.style.height = this.getDocHeight( doc ) * 1.5 + "px";
        ifrm.style.visibility = 'visible';
      }
    }
  },
  computed: {
    sameOrigin: function () {
      if (this.src.substr(0,4) != 'http') return true;
      var mylocation=window.location || window.document.location;
      let extHost=new URL(this.src).hostname;
      return (mylocation.hostname == extHost);
    }
  }
}
</script>