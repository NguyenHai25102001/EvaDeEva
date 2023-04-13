var AT = {
    JET_LAG: 200, // 200 miniseconds
    cookie_domain: null,
    cookie_duration: 30, // 30 days
    is_reoccur: 1,
    is_lastclick: 1,
    success_page: "thank_you",
    shop_id: null,
    aff_source_param: "utm_source",


    track: function () {
        var sid = this.get_param("aff_sid");
        var utm_source = this.get_param(this.aff_source_param);

        if (this.is_lastclick) {
            if (utm_source) {
                this.set_cookie("_aff_network", utm_source, this.cookie_duration);
                if (sid) {
                    this.set_cookie("_aff_sid", sid, this.cookie_duration);
                } else {
                    this.set_cookie("_aff_sid", "", -3000);
                }
            }
        } else {
            var cookie_utm_source = this.get_cookie("_aff_network");

            if (!cookie_utm_source && utm_source) {
                this.set_cookie("_aff_network", utm_source, this.cookie_duration);
                if (sid) {
                    this.set_cookie("_aff_sid", sid, this.cookie_duration);
                } else {
                    this.set_cookie("_aff_sid", "", -3000);
                }
            }
            if (utm_source == "accesstrade" && cookie_utm_source == "accesstrade") {
                this.set_cookie("_aff_network", "accesstrade", this.cookie_duration);
                if (sid) {
                    this.set_cookie("_aff_sid", sid, this.cookie_duration);
                }
            }
        }
    },

    track_haravan_thank_you_page: function () {
        console.log("[AT] Tracking conversion...");
        var pathArray = window.location.pathname.split('/');
        var last_part = pathArray[pathArray.length - 1];
        var get_utm_source = this.get_cookie("_aff_network");

        if (last_part == this.success_page && get_utm_source == "accesstrade") {
            var sid = this.get_cookie('_aff_sid');
            if (!sid) {
                console.log("[AT] There is no session id found");
                return false;
            }

            if (typeof Haravan.checkout === "undefined") {
                console.log("[AT] There is no Haravan order info");
                return false;
            }

            var identifier = Haravan.checkout.order_id;
            var param = [];
            param.push("identifier=" + encodeURIComponent(identifier));
            param.push("shop_id=" + encodeURIComponent(this.shop_id));
            param.push("sid=" + encodeURIComponent(sid));
            var a = document.createElement("img");
            a.width = 1;
            a.height = 1;
            a.border = 0;
            a.src = "https://track.accesstrade.vn/track/haravan/tracking/omni?" + param.join("&");
            console.log("[AT] Pixel tracking url: " + a.src);
            document.body.appendChild(a);

            if (!this.is_reoccur) {
                this.set_cookie("_aff_network", "", -3000);
                this.set_cookie("_aff_sid", "", -3000);
            }
        }
    },

    get_param: function (name, url) {
        if (!url) url = location.href;
        name = name.replace(/[\[]/, "\\\[").replace(/[\]]/, "\\\]");
        var regexS = "[\\?&]" + name + "=([^&#]*)";
        var regex = new RegExp(regexS);
        var results = regex.exec(url);
        return results == null ? null : results[1];
    },

    set_cookie: function (n, v, e) {
        var d = new Date();
        d.setTime(d.getTime() + (e * 24 * 60 * 60 * 1000));
        var ee = "expires=" + d.toUTCString();
        cookie_domain = this.cookie_domain || window.location.hostname;
        document.cookie = n + "=" + v + "; " + ee + ";domain=" + cookie_domain + "; path=/";
    },

    get_cookie: function (cname) {
        var name = cname + "=";
        var ca = document.cookie.split(';');
        for (var i = 0; i < ca.length; i++) {
            var c = ca[i];
            while (c.charAt(0) == ' ') c = c.substring(1);
            if (c.indexOf(name) == 0) return c.substring(name.length, c.length);
        }
        return null;
    },

     get_script_qs: function() {
        var target = document.currentScript || (function() {
            var scripts = document.getElementsByTagName('script');
            var last_script = scripts[scripts.length - 1];
            if( last_script.src != "" && last_script.src.indexOf("/haravan_tracking_v1.js") > -1 ) {
                return last_script;
            }
            for (var i = 0; i < scripts.length; i++) {
                if (scripts[i].src.indexOf("/haravan_tracking_v1.js") > -1) {
                    return scripts[i];
                }
            }
            return {src: ''};
        })();
        return target.src.replace(/^[^\?]+\??/,'');
    },

    init: function () {
        var query_string = this.get_script_qs();
        var pa = query_string.split("?").pop().split("&");
        var kv = [];
        for (var j = 0; j < pa.length; j++) {
            kv = pa[j].split("=");
            switch (kv[0]) {
                case "cookie_duration":
                    this.cookie_duration = parseInt(kv[1]);
                    break;
                case "is_reoccur":
                    this.is_reoccur = parseInt(kv[1]);
                    break;
                case "is_lastclick":
                    this.is_lastclick = parseInt(kv[1]);
                    break;
                case "success_page":
                    this.success_page = kv[1];
                    break;
                case "shop_id":
                    this.shop_id = kv[1];
                    break;
                case "cookie_domain":
                    this.cookie_domain = kv[1];
                    break;
                case "aff_source_param":
                    this.aff_source_param = kv[1];
                    break;
            }
        }
    },
};

AT.init();
AT.track();
AT.track_haravan_thank_you_page();
