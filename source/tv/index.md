---
uuid: 2691a4f0-2a79-11ea-8486-776fa8141052
title: 电视直播
date: 2019-01-01 08:00:00
comments: false
---

<link rel="stylesheet" href="https://cdn.bootcss.com/video.js/7.6.0/video-js.min.css" />
<select id="selector" style="display: block;margin: 10px auto;"></select>
<video id="player" class="video-js vjs-big-play-centered vjs-16-9" autoplay controls preload="auto" data-setup="{}">
  <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a web browser that
    <a href="https://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
  </p>
</video>
<script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
<script src="https://cdn.bootcss.com/video.js/7.6.0/video.min.js"></script>
<script src="https://cdn.bootcss.com/videojs-flash/2.2.0/videojs-flash.min.js"></script>
<script>
  var sources = [
    {
      name: "全国风景总览",
      src: "https://gcalic.v.myalicdn.com/gc/wgw05_1/index.m3u8?contentid=2820180516001",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV1",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv1_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV2",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv2_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV3",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv3_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV4",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv4_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV6",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv6_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV7",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv7_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV8",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv8_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV9",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctvjilu_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV10",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv10_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV11",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv11_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV12",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv12_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV13新闻频道",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv13_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV14少儿频道",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctvchild_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "CCTV15音乐频道",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/live/cctv15_2_hd.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "北京卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/btv1_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "东方卫视",
      src: "http://cctvtxyh5c.liveplay.myqcloud.com/wstv/dongfang_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "广东卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/guangdong_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "江苏卫视",
      src: "http://live2.plus.hebtv.com/jsws/sd/live.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "重庆卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/chongqing_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "四川卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/sichuan_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "山东卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/shandong_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "天津卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/tianjin_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "山西卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/shan1xi_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "旅游卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/travel_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "湖南卫视",
      src: "rtmp://58.200.131.2:1935/livetv/hunantv",
      type: "rtmp/flv"
    },
    {
      name: "湖北卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/hubei_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "东南卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/dongnan_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "云南卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/yunnan_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "河北卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/hebei_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "河南卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/henan_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "浙江卫视",
      src: "http://live4.plus.hebtv.com/zjws/sd/live.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "安徽卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/anhui_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "黑龙江卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/heilongjiang_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "江西卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/jiangxi_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "陕西卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/shan3xi_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "辽宁卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/liaoning_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "吉林卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/jilin_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "新疆卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/xinjiang_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "兵团卫视",
      src: "http://39.134.52.157/hwottcdn.ln.chinamobile.com/PLTV/88888890/224/3221226028/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "广西卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/guangxi_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "青海卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/qinghai_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "甘肃卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/gansu_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "内蒙古卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/neimenggu_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "宁夏卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/ningxia_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "西藏卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/xizang_2/index.m3u8",
      type: "application/x-mpegURL"
    },
    {
      name: "深圳卫视",
      src: "http://cctvalih5c.v.myalicdn.com/wstv/shenzhen_2/index.m3u8",
      type: "application/x-mpegURL"
    }
  ];
  $(function () {
    var player = videojs("player");
    var playlist = sources || [];
    var element = "<option>请选择电视频道</option>";
    for (var i = 0; i < playlist.length; i++) {
      element += '<option value="' + i + '">' + playlist[i].name + "</option>";
    }
    $("#selector").append(element).change(function () {
      player.src(playlist[+$("#selector").val()]);
    });
  });
</script>
