# Analytics Javascript
This Javascript file is a framework for creating an analytics integration with Optimizely. When you want to create an integration with Optimizely, this file has to be added to all the pages of the website, below the Optimizely snippet. The file analytics.js is a working example for a Google Analytics integration. The only functions that need to be modified to get an integration working with any other Analytics tool are: 

```javascript
/**
 * The integration object is modified to integrate with Google Analytics.
 */
window.integration = {
  /**
   * This function sets a custom variable on Google Analytics for every experiment
   * that is active on this page.The function is called for all active experiments,
   * including any redirect experiments that might have been running on a previous
   * page.
   *
   * @param {string} experimentId
   * @param {string} variationId
   */
  makeRequest: function (experimentId, variationIds) {
    var slot = 2;
    var keyValue = window.integrator.makeSendableNames(experimentId, variationIds, 255, 255, 255, false, "test");

    window["_gaq"].push(["_setCustomVar", slot, keyValue.key, keyValue.value, 2]);
  },

  /**
   * This function makes sure that the correct referrer value is send
   * to Google Analytics. If a redirect experiment has happend, the
   * referrer value needs to be used from the previous page. The
   * referrer value is stored in a redirect cookie. This function
   * is only called once for every page.
   */
  initialize: function () {
    window["_gaq"] = window["_gaq"] || [];

    var referrer = window.integrator.redirect.getRedirectReferrer();
    if (referrer !== null) {
      window["_gaq"].push(['_setReferrerOverride', referrer]);
    }
  }
};
```    
    
The rest of the the code takes care of the rest:

 * Retrieve all running experiments and their variations form the `optimizely.variationIdsMap` function. 
 * Determine if you are looking at a page was the result of a redirect experiment. In most cases, the experiment doesn't run on the page where you are redirected to, so a redirect experiment can only be detected by looking at a redirect cookie that Optimizely sets right before a redirect occurs.  
 * A function that abstracts the original referrer url before a redirect happened. This can be used to correct information in your analytics tool (see GA example).
 * A function that creates sendable names based on integration specific paramters. It makes sure that information that is being send is not too long and doesn't contain any invalid characters.

