<template >
  <div class="d-flex flex-column flex-root">
    <form class="form w-100" novalidate="novalidate" id="kt_sign_in_form"
      data-kt-redirect-url="../../demo3/dist/index.html" action="#">
      <div class="text-center mb-10">
      </div>
      <div class="text-center">
        <div id="buttonDiv"></div>
      </div>
    </form>
  </div>
</template>


<script>
import axios from "axios";
import jwt_decode from "jwt-decode";
// @ is an alias to /src
export default {
  name: "HomeView",
  data() {
    return {};
  },
  methods: {},
  mounted() {
    function handleCredentialResponse(response) {
      let token = response.credential;
      var decoded = jwt_decode(token);
      send(decoded);
    }
    function send(decoded) {
      let name = decoded.given_name;
      let email = decoded.email;
      let id_token = decoded.jti;
      let imageUrl = decoded.picture;
      let sname = decoded.family_name;
      axios
        .post(import.meta.env.VITE_API_URL + "/api/sign", {
          email: email,
          name: name,
          sname: sname,
          imageUrl: imageUrl,
          id_token: id_token,
        })
        .then(function (response) {
          // handle success
          response;
          document.cookie =
            "dyns_token=" +
            id_token +
            "; path=/; expires=Tue, 19 Jan 2038 03:14:07 GMT";
          window.location.href = "/";
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        //  window.location.href = "/sign";
        });
    }
    window.onload = function () {
     
      console.log(import.meta.env.VITE_API_APP_GCLIENT)
      google.accounts.id.initialize({
        client_id: import.meta.env.VITE_API_APP_GCLIENT,
        callback: handleCredentialResponse,
      });
      google.accounts.id.renderButton(
        document.getElementById("buttonDiv"),
        { theme: "outline", size: "large" } // customization attributes
      );
      google.accounts.id.prompt(); // also display the One Tap dialog
    };
  },
  components: {},
};
</script>
