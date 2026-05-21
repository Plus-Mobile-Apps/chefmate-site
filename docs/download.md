# Download Chef Mate

Get the latest desktop release of Chef Mate for your platform.

<p id="latest-release-version"><em>Loading latest release…</em></p>

<div class="download-buttons" markdown>

[:fontawesome-brands-apple: macOS (.dmg)](https://github.com/Plus-Mobile-Apps/chef-mate/releases/latest){ .md-button .btn-mac #download-mac }
[:fontawesome-brands-windows: Windows (.msi)](https://github.com/Plus-Mobile-Apps/chef-mate/releases/latest){ .md-button .btn-windows #download-windows }
[:fontawesome-brands-linux: Linux (.deb)](https://github.com/Plus-Mobile-Apps/chef-mate/releases/latest){ .md-button .btn-linux #download-linux }

</div>

Looking for an older version or the Android APK? Visit the [full releases page on GitHub](https://github.com/Plus-Mobile-Apps/chef-mate/releases).

<script>
(function () {
  var patterns = {
    "download-mac": /^Chef\.Mate-.*\.dmg$/,
    "download-windows": /^Chef\.Mate-.*\.msi$/,
    "download-linux": /^chef-mate_.*_amd64\.deb$/
  };

  fetch("https://api.github.com/repos/Plus-Mobile-Apps/chef-mate/releases/latest")
    .then(function (res) {
      if (!res.ok) throw new Error("GitHub API " + res.status);
      return res.json();
    })
    .then(function (release) {
      Object.keys(patterns).forEach(function (id) {
        var asset = (release.assets || []).find(function (a) {
          return patterns[id].test(a.name);
        });
        var el = document.getElementById(id);
        if (asset && el) el.href = asset.browser_download_url;
      });
      var caption = document.getElementById("latest-release-version");
      if (caption && release.tag_name) {
        caption.innerHTML = "Latest release: <strong>" + release.tag_name + "</strong>";
      }
    })
    .catch(function () {
      var caption = document.getElementById("latest-release-version");
      if (caption) {
        caption.innerHTML = 'Could not fetch the latest version. <a href="https://github.com/Plus-Mobile-Apps/chef-mate/releases/latest">View all releases on GitHub</a>.';
      }
    });
})();
</script>
