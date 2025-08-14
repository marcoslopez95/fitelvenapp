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
// Para mayor claridad y si el tipado estricto es importante, puedes definirla así:
interface CustomLoadFinishedEventData extends EventData {
  object: WebView;
  url: string;
  error?: string; // Si hay un error de carga
}

export default Vue.extend({
  computed: {
    message() {
      return "Blank {N}-Vue app";
    }
  },
  methods: {
    onWebViewNavigation(args: any) {
      const url = args.url;
      try {
        const parsedUrl = new URL(url);
        // Verifica que el hostname termine en .fitelven.com.ve (subdominio)
        const isSubdomain = parsedUrl.hostname.endsWith('.fitelven.com.ve');
        if (!isSubdomain) return;
        openUrl(url);
        args.object.stopLoading && args.object.stopLoading();
        if (isAndroid) {
          android.os.Process.killProcess(android.os.Process.myPid());
        }
      } catch (e) {
        // URL no válida
        return;
      }

    },
    onWebViewLoaded(args: CustomLoadFinishedEventData) {
      const webView = args.object as WebView;

      if (isAndroid) {
        const androidWebView = webView.android; // Acceder a la instancia nativa de Android WebView

        const webSettings = androidWebView.getSettings();
        webSettings.setDomStorageEnabled(true); // Habilitar DOM Storage (para localStorage)
        webSettings.setJavaScriptEnabled(true); // Asegúrate de que JavaScript esté habilitado
        webSettings.setDatabaseEnabled(true); // También puede ser útil para algunas cosas

        // Habilitar cookies
        const cookieManager = android.webkit.CookieManager.getInstance();
        cookieManager.setAcceptCookie(true);
        if (android.os.Build.VERSION.SDK_INT >= 21) { // Para Android 5.0 (Lollipop) y superior
          cookieManager.setAcceptThirdPartyCookies(androidWebView, true);
        }

      } else if (isIOS) {
        const iosWebView = webView.ios; // Acceder a la instancia nativa de iOS WKWebView

        // WKWebView habilita localStorage y cookies por defecto de forma más robusta
        // Sin embargo, si tienes problemas, podrías investigar WKWebsiteDataStore.
        // WKWebsiteDataStore.default().fetchDataRecordsOfTypes(WKWebsiteDataStore.allWebsiteDataTypes(), (records) => {
        //   records.forEach((record) => {
        //     console.log(`Data Record: ${record.displayName}`);
        //   });
        // });
        // Para iOS 11+, puedes usar:
        // let config = iosWebView.configuration;
        // if (config && config.websiteDataStore) {
        //     config.websiteDataStore = WKWebsiteDataStore.default();
        // }
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
  horizontal-align: center;
  vertical-align: center;
}
</style>
