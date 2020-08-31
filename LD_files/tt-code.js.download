var ttLanding = {
    ttOriginParam: 'ttorigin',
    outNodes: [],
    scriptParams: null,
    apiMeta: {
        request: false,
        response: null,
    },
    httpGetAsync: function (url, callback) {
        var xmlHttp = new XMLHttpRequest();
        xmlHttp.onreadystatechange = function () {
            if (xmlHttp.readyState == 4 && xmlHttp.status == 200)
                callback(xmlHttp.responseText);
        };
        xmlHttp.open("GET", url, true);
        xmlHttp.send(null);
    },
    getQueryVariable: function (variable) {
        var query = window.location.search.substring(1);
        var vars = query.split('&');
        for (var i = 0; i < vars.length; i++) {
            var pair = vars[i].split('=');
            if (pair[0] == variable) return pair[1];
        }
        return false;
    },
    appendToOut: function (cid) {
        for (var i = 0; i < ttLanding.outNodes.length; i++) {
            var link = new URL(ttLanding.outNodes[i].href);
            var and = link.search !== '' ? '&' : '?';
            var out = link + and + ttLanding.ttOriginParam + '=' + cid;
            ttLanding.outNodes[i].href = out;
        }
    },
    removeOrigin: function (origin) {
        var uri = window.location.toString();
        var and = '&';
        if (uri.indexOf('?' + ttLanding.ttOriginParam) !== -1) and = '?';
        var replace = and + ttLanding.ttOriginParam + '=' + origin;
        var newUri = uri.replace(replace, '');
        window.history.replaceState({}, document.title, newUri);
    },
    getOutNodes: function () {
        var allNodes = document.getElementsByTagName('a');
        for (var i = 0; i < allNodes.length; i++) {
            var href = document.getElementsByTagName("a")[i].href;
            if (href.indexOf('/outgoing') !== -1 || href.indexOf('/path/out.php') !== -1) {
                ttLanding.outNodes.push(allNodes[i])
            }
        }
    },
    getScriptParams: function () {
        var script = document.getElementById('ttcode');
        ttLanding.scriptParams = {
            domain: script.getAttribute('domain'),
            campaignId: script.getAttribute('campaign'),
            landingId: script.getAttribute('landing'),
            parallelTracking: script.getAttribute('parallel'),
            externalParam: script.getAttribute('external-param'),
        }
    },
    makeClick: function () {
        if (ttLanding.scriptParams == null) return;

        var parallel = ttLanding.scriptParams.parallelTracking === 'true' ? 1 : 0;
        if (parallel == 1) {
          var external = ttLanding.getQueryVariable(ttLanding.scriptParams.externalParam);
          if (external == false || external == "") parallel = 0;
        }

        var query = window.location.search.replace('?', '&');
        var uri = ttLanding.scriptParams.domain + '/landing?trvid=' + ttLanding.scriptParams.campaignId +
            '&trvrf=' + encodeURIComponent(document.referrer) + '&lpid=' +
            ttLanding.scriptParams.landingId + '&parallel=' + parallel + query;

        ttLanding.apiMeta.request = true;
        ttLanding.httpGetAsync(uri, function (response) {
            response = JSON.parse(response);
            ttLanding.apiMeta.response = response;
            if (response.clickId !== undefined && response.clickId !== '')
                ttLanding.appendToOut(response.clickId)
        });
    },
    debug: function () {
        console.log({
            scriptParams: ttLanding.scriptParams,
            outNodes: ttLanding.outNodes,
            apiMeta: ttLanding.apiMeta
        });
    },
    init: function () {
        ttLanding.getScriptParams();
        ttLanding.getOutNodes();

        var origin = ttLanding.getQueryVariable(ttLanding.ttOriginParam);
        if (origin && origin !== '') {
            ttLanding.removeOrigin(origin);
            ttLanding.appendToOut(origin);
            return;
        }
        ttLanding.makeClick()
    }
};
ttLanding.init();
