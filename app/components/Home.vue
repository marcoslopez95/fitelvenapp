<template>
  <Page :actionBarHidden="true">
    <GridLayout>
      <WebView src="https://maizbistro.makeidpro.com" @loadStarted="onWebViewNavigation" />
    </GridLayout>
  </Page>
</template>

<script lang="ts">
import Vue from "nativescript-vue";
import { WebView } from '@nativescript/core/ui/web-view';
import { EventData } from '@nativescript/core/data/observable';
import { isAndroid, isIOS } from '@nativescript/core/platform';
import { openUrl } from '@nativescript/core/utils';
import { Dialogs,Utils } from '@nativescript/core';
import { InAppBrowser } from 'nativescript-inappbrowser';

export default Vue.extend({
  computed: {
    message() {
      return "Blank {N}-Vue app";
    }
  },
  methods: {
    async onWebViewNavigation(args: any) {
      const url = args.url;
      try {
      if (await InAppBrowser.isAvailable()) {
        const result = await InAppBrowser.open(url, {
          // iOS Properties
          dismissButtonStyle: 'cancel',
          preferredBarTintColor: '#453AA4',
          preferredControlTintColor: 'white',
          readerMode: false,
          animated: true,
          modalPresentationStyle: 'fullScreen',
          modalTransitionStyle: 'coverVertical',
          modalEnabled: true,
          enableBarCollapsing: false,
          // Android Properties
          showTitle: true,
          toolbarColor: '#6200EE',
          secondaryToolbarColor: 'black',
          navigationBarColor: 'black',
          navigationBarDividerColor: 'white',
          enableUrlBarHiding: true,
          enableDefaultShare: true,
          forceCloseOnRedirection: false,
          // Specify full animation resource identifier(package:anim/name)
          // or only resource name(in case of animation bundled with app).
          animations: {
            startEnter: 'slide_in_right',
            startExit: 'slide_out_left',
            endEnter: 'slide_in_left',
            endExit: 'slide_out_right'
          },
          headers: {
            'my-custom-header': 'my custom header value'
          },
          hasBackButton: true,
          browserPackage: '',
          showInRecents: false
        });
        Dialogs.alert({
          title: 'Response',
          message: JSON.stringify(result),
          okButtonText: 'Ok'
        });
      }
      else {
        Utils.openUrl(url);
      }
    }
    catch(error) {
      Dialogs.alert({
        title: 'Error',
        message: error.message,
        okButtonText: 'Ok'
      });
    }
    }
  }
});
</script>

<style scoped lang="scss">
@import '@nativescript/theme/scss/variables/blue';

// Custom styles
.fas {
  @include colorize($color: accent);
}

.info {
  font-size: 20;
  text-align: center;
  vertical-align: middle;
}
</style>
