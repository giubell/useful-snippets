# useful-snippets
A collection of useful js snippets

#################################
## XMLHTTP REQUEST INTERCEPTOR ##
#################################

let XHRSend = XMLHttpRequest.prototype.send;
XMLHttpRequest.prototype.send = function(data) {
    this.setRequestHeader('Authorization', 'Bearer ' + token); // you can add a token to header
    this.setRequestHeader('Cache-Control', 'no-cache'); // or something else
    return XHRSend.call(this, data);
};

##################################
## XMLHTTP RESPONSE INTERCEPTOR ##
##################################
// Example of response interceptor of a expired token
let oldXHROpen = XMLHttpRequest.prototype.open;
let isRefreshingToken = this.isRefreshingToken;
XMLHttpRequest.prototype.open = function(method, url, async, user, password) {
// do something with the method, url and etc.
  this.addEventListener('load', function() {
    // do something with the response text
    if (this.status === 401) {
        if (!isRefreshingToken) {
          isRefreshingToken = true;
          return oauth2.refreshToken()
            .take(1)
            .subscribe((token) => {
              isRefreshingToken = false;
              location.reload();
              },
              ((err) => {
                store.dispatch(new LogoutAction({}));
                return Observable.of(err);
              })
            );
        }
      }
  });

  return oldXHROpen.apply(this, arguments);
}
