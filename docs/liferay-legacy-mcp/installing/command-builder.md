---
title: Command Builder
parent: Installing
nav_order: 13
---

# Command Builder
{: .no_toc }

Answer a few questions, and copy the exact command or settings for your setup. Everything happens in your browser: nothing you type here is sent anywhere.

{: .important }
First, [create the service user](liferay-setup.html), and deploy the OAuth 2 client extension as on [Tomcat](tomcat.html), [Docker](docker.html) or [PaaS](paas.html). This page builds what comes after: running the server, and adding it to your AI assistant.

<div class="lmcp-builder" id="lmcp-builder">
  <style>
    .lmcp-builder { margin: 1.5rem 0; }
    .lmcp-builder fieldset { border: 1px solid #d9dee8; border-radius: 8px; padding: 0.75rem 1rem 1rem; margin: 0 0 1rem; }
    .lmcp-builder legend { font-weight: 600; padding: 0 0.35rem; }
    .lmcp-builder label { display: block; margin: 0.35rem 0; }
    .lmcp-builder .lmcp-hint { display: block; font-size: 0.875em; color: #5b6576; margin: 0.15rem 0 0.4rem 1.6rem; }
    .lmcp-builder input[type=text] { width: 100%; max-width: 34rem; padding: 0.4rem 0.55rem; border: 1px solid #c3cad6; border-radius: 6px; font: inherit; }
    .lmcp-builder .lmcp-field { margin: 0.6rem 0 0.2rem; }
    .lmcp-builder .lmcp-field > span { display: block; font-weight: 600; margin-bottom: 0.25rem; }
    .lmcp-builder .lmcp-output { border: 2px solid #0b5fff; border-radius: 8px; padding: 0.75rem 1rem 1rem; }
    .lmcp-builder pre { position: relative; white-space: pre-wrap; word-break: break-all; }
    .lmcp-builder .lmcp-copy { float: right; margin: 0 0 0.4rem 0.5rem; padding: 0.25rem 0.7rem; border: 1px solid #c3cad6; border-radius: 6px; background: #fff; cursor: pointer; font: inherit; font-size: 0.875em; }
    .lmcp-builder .lmcp-warn { background: #fff6e0; border-left: 4px solid #e0a100; padding: 0.5rem 0.75rem; margin: 0.6rem 0; }
    .lmcp-builder [hidden] { display: none !important; }
  </style>

  <form id="lmcp-form" autocomplete="off" onsubmit="return false">
    <fieldset>
      <legend>1. Where does Liferay run?</legend>
      <label><input type="radio" name="host" value="remote" checked> At an address, such as <code>https://liferay.example.com</code> (Tomcat, PaaS, SaaS)</label>
      <label><input type="radio" name="host" value="docker"> In Docker, on this computer</label>
      <div class="lmcp-field" data-show="host=remote">
        <span>Liferay's address</span>
        <input type="text" name="url" value="https://liferay.example.com">
      </div>
      <div class="lmcp-field" data-show="host=docker">
        <span>Liferay's container name</span>
        <input type="text" name="container" value="liferay">
        <span class="lmcp-hint">The server joins its network, and reads the credentials from its <code>liferay-routes</code> volume, as in <a href="docker.html">Liferay Self-Hosted (Docker)</a>.</span>
      </div>
    </fieldset>

    <fieldset>
      <legend>2. Who uses it?</legend>
      <label><input type="radio" name="mode" value="personal" checked> Me, from an AI assistant on my computer</label>
      <label><input type="radio" name="mode" value="shared"> Services and people, through one shared server over HTTPS</label>
    </fieldset>

    <fieldset data-show="mode=personal">
      <legend>3. Which AI assistant?</legend>
      <label><input type="radio" name="client" value="claude-code" checked> Claude Code</label>
      <label><input type="radio" name="client" value="claude-desktop"> Claude desktop app (its chats)</label>
      <label><input type="radio" name="client" value="copilot"> GitHub Copilot (Visual Studio Code and the Copilot CLI)</label>
      <label><input type="radio" name="client" value="codex"> Codex</label>
      <label><input type="radio" name="client" value="other"> Another AI assistant that reads <code>mcpServers</code></label>
    </fieldset>

    <fieldset>
      <legend>4. Connection</legend>
      <label><input type="radio" name="auth" value="oauth" checked> OAuth 2, through the client extension (recommended)</label>
      <label data-show="host=remote"><input type="radio" name="auth" value="password"> The service user's password, for 7.4 updates before U92</label>
      <div class="lmcp-field" data-show="auth=oauth,host=remote">
        <span>Folder that holds the <code>liferay-mcp-oauth</code> folder</span>
        <input type="text" name="routes" value="/Users/me/liferay-mcp/routes">
        <span class="lmcp-hint">Its full path, without <code>~</code>. See the page for where your Liferay runs for how to copy it.</span>
      </div>
      <div class="lmcp-field" data-show="auth=password,host=remote">
        <span>Environment file with <code>LIFERAY_USERNAME</code> and <code>LIFERAY_PASSWORD</code></span>
        <input type="text" name="envfile" value="/Users/me/liferay-mcp/connection.env">
        <span class="lmcp-hint">Create it as in <a href="../reference/settings.html#the-password-connection">The Password Connection</a>, readable only by you.</span>
      </div>
    </fieldset>

    <fieldset>
      <legend>5. Options</legend>
      <div class="lmcp-field" data-show="mode=personal">
        <span>Site to work in by default (optional)</span>
        <input type="text" name="site" value="my-site">
      </div>
      <div class="lmcp-field" data-show="mode=shared">
        <span>The server's public HTTPS address</span>
        <input type="text" name="public" value="https://mcp.example.com">
      </div>
      <div class="lmcp-field">
        <span>Image</span>
        <label><input type="radio" name="image" value="slim" checked> <code>slim</code>: every tool except page inspection, about 350 MB</label>
        <label><input type="radio" name="image" value="full"> <code>full</code>: also page inspection and screenshots, about 1.2 GB</label>
      </div>
      <div class="lmcp-field">
        <span>Updates</span>
        <label><input type="radio" name="updates" value="pinned" checked> Stay on 1.4.0, and upgrade by hand</label>
        <label><input type="radio" name="updates" value="auto"> Use each new 1.x release automatically (<a href="upgrading-and-removing.html#updating-automatically">how</a>)</label>
      </div>
      <div class="lmcp-field" data-show="mode=personal">
        <span>Writes</span>
        <label><input type="radio" name="writes" value="off" checked> Off: the assistant can only read</label>
        <label><input type="radio" name="writes" value="confirm"> On, each change previewed and confirmed (<a href="../using/writes.html">writes</a>)</label>
      </div>
    </fieldset>
  </form>

  <div class="lmcp-output" aria-live="polite">
    <div id="lmcp-steps"></div>
  </div>

  <script>
  (function () {
    var VERSION = "1.4.0";
    var IMAGE = "ghcr.io/liferay-community-apac/liferay-legacy-mcp";
    var root = document.getElementById("lmcp-builder");
    var form = document.getElementById("lmcp-form");
    var out = document.getElementById("lmcp-steps");

    function value(name) {
      var field = form.elements[name];
      return field && field.value !== undefined ? String(field.value).trim() : "";
    }

    function escapeHtml(text) {
      return text.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    }

    // Shows the elements whose data-show conditions ("name=value,name=value") all hold.
    function applyVisibility() {
      root.querySelectorAll("[data-show]").forEach(function (element) {
        var visible = element.getAttribute("data-show").split(",").every(function (condition) {
          var parts = condition.split("=");
          return value(parts[0]) === parts[1];
        });
        element.hidden = !visible;
      });
      // The password connection is only offered for Liferay at an address.
      if (value("host") === "docker" && value("auth") === "password") {
        form.querySelector('input[name=auth][value=oauth]').checked = true;
      }
    }

    // The docker run arguments, after "docker run", as a list.
    function dockerArgs(c) {
      var args = [];
      if (c.shared) {
        args.push("-d", "--name", "liferay-mcp", "--restart", "unless-stopped");
      } else {
        args.push("-i", "--rm");
      }
      if (c.auto) args.push("--pull=always");
      if (c.docker) {
        args.push("--network", "container:" + c.container);
        args.push("-v", "liferay-routes:/opt/liferay/routes:ro");
      } else if (c.shared) {
        args.push("-p", "127.0.0.1:3333:3333");
      }
      if (!c.docker && c.auth === "oauth") args.push("-v", c.routes + ":/run/liferay-routes:ro");
      if (c.auth === "password") args.push("--env-file", c.envfile);
      if (c.shared) args.push("-v", "/srv/liferay-mcp/secrets:/run/mcp-secrets:ro");
      args.push("-v", "liferay-mcp-data:/data");
      args.push("-e", "LIFERAY_URL=" + (c.docker ? "http://localhost:8080" : c.url));
      if (c.auth === "oauth") {
        args.push("-e", "LIFERAY_ROUTES_CLIENT_EXTENSION=" + (c.docker ? "/opt/liferay/routes/default/liferay-mcp-oauth" : "/run/liferay-routes/liferay-mcp-oauth"));
      }
      if (!c.shared && c.site) args.push("-e", "LIFERAY_DEFAULT_SITE=" + c.site);
      if (!c.shared && c.writes === "confirm") args.push("-e", "MCP_WRITE_MODE=confirm");
      if (c.shared) {
        args.push("-e", "MCP_HTTP_HOST=0.0.0.0");
        args.push("-e", "MCP_PUBLIC_URL=" + c.publicUrl);
        args.push("-e", "MCP_TRUST_PROXY=private");
        args.push("-e", "MCP_HTTP_API_KEYS_FILE=/run/mcp-secrets/api-keys");
      }
      args.push(IMAGE + ":" + c.tag);
      if (c.shared) args.push("--http");
      return args;
    }

    // Shell: one option and its value per line.
    function shell(prefix, args) {
      var lines = [];
      var first = [];
      var i = 0;
      // The flags without values that start the command stay on the first line.
      while (i < args.length && /^(-i|--rm|-d|--pull=always)$/.test(args[i])) first.push(args[i++]);
      while (i < args.length && /^(--name|--restart)$/.test(args[i])) { first.push(args[i], args[i + 1]); i += 2; }
      // As in the guide's pages: the network (or published port) on the first line too.
      if (i < args.length && /^(--network|-p)$/.test(args[i])) { first.push(args[i], args[i + 1]); i += 2; }
      while (i < args.length) {
        if (/^-/.test(args[i]) && i + 1 < args.length && !/^-/.test(args[i + 1]) && args[i] !== "--http") {
          lines.push(args[i] + " " + args[i + 1]);
          i += 2;
        } else if (args[i] === "--http") {
          lines[lines.length - 1] += " --http";
          i += 1;
        } else {
          lines.push(args[i]);
          i += 1;
        }
      }
      return [prefix + "docker run " + first.join(" ")].concat(lines).join(" \\\n  ");
    }

    // JSON args: the leading flags together, then one option and its value per line.
    function jsonArgs(args, indent) {
      var rows = [];
      var head = [];
      var i = 0;
      while (i < args.length && /^(-i|--rm|--pull=always)$/.test(args[i])) head.push(args[i++]);
      rows.push(["run"].concat(head));
      while (i < args.length) {
        if (/^-/.test(args[i]) && i + 1 < args.length) { rows.push([args[i], args[i + 1]]); i += 2; }
        else { rows.push([args[i]]); i += 1; }
      }
      return rows.map(function (row) {
        return indent + row.map(function (item) { return JSON.stringify(item); }).join(", ");
      }).join(",\n");
    }

    function block(language, text) {
      return '<pre><button type="button" class="lmcp-copy">Copy</button><code class="language-' + language + '">' + escapeHtml(text) + "</code></pre>";
    }

    function build() {
      applyVisibility();
      var c = {
        docker: value("host") === "docker",
        shared: value("mode") === "shared",
        client: value("client"),
        auth: value("host") === "docker" ? "oauth" : value("auth"),
        url: value("url") || "https://liferay.example.com",
        container: value("container") || "liferay",
        routes: value("routes") || "/Users/me/liferay-mcp/routes",
        envfile: value("envfile") || "/Users/me/liferay-mcp/connection.env",
        site: value("site"),
        publicUrl: value("public") || "https://mcp.example.com",
        auto: value("updates") === "auto",
        writes: value("writes"),
      };
      c.tag = (c.auto ? "1" : VERSION) + "-" + value("image");
      var args = dockerArgs(c);
      var html = [];
      var warnings = [];
      var jsonFile = (c.client === "copilot" || c.client === "claude-desktop" || c.client === "other");

      if (/^~/.test(c.routes) && c.auth === "oauth" && !c.docker && (c.shared || jsonFile)) {
        warnings.push("Give the folder's full path: <code>~</code> isn't expanded here.");
      }
      if (/^~/.test(c.envfile) && c.auth === "password" && (c.shared || jsonFile)) {
        warnings.push("Give the environment file's full path: <code>~</code> isn't expanded here.");
      }
      if (c.shared && c.docker === false && /localhost|127\.0\.0\.1/.test(c.url)) {
        warnings.push("A shared server in its own container can't reach Liferay at <code>localhost</code>. On the Liferay machine, see <a href=\"tomcat.html#step-3-run-the-server\">Tomcat</a> or <a href=\"docker.html#step-3-run-the-server\">Docker</a>.");
      }

      if (c.shared) {
        html.push("<h3>1. Create the API keys file</h3>",
          "<p>Make <code>/srv/liferay-mcp/secrets/api-keys</code> as in <a href=\"http.html#step-2-give-services-api-keys\">Give Services API Keys</a>.</p>",
          "<h3>2. Start the server</h3>",
          block("bash", shell("", args)),
          "<p>It listens on <code>127.0.0.1:3333</code>. Put your HTTPS reverse proxy for <code>" + escapeHtml(c.publicUrl) + "</code> in front of it, and see <a href=\"http.html\">For Services and People</a> for sign-in with Liferay. Writes stay off over HTTPS unless you turn them on there.</p>");
      } else if (c.client === "claude-code" || c.client === "codex") {
        var tool = c.client === "claude-code" ? "claude" : "codex";
        html.push("<h3>1. Add the server</h3>",
          "<p>Run this once in a terminal:</p>",
          block("bash", shell(tool + " mcp add liferay-legacy -- ", args)));
        if (c.client === "claude-code") {
          html.push("<p>To use it in every folder, and in the Claude desktop app's Code tab, add <code>-s user</code> after <code>add</code>. More in <a href=\"claude-code.html\">With Claude Code</a>.</p>");
        } else {
          html.push("<p>Pull the image first, so the first start doesn't run past Codex's 30-second limit: <code>docker pull " + IMAGE + ":" + c.tag + "</code>. More in <a href=\"codex.html\">With Codex</a>.</p>");
        }
        html.push("<h3>2. Check that it connects</h3>",
          block("bash", tool + " mcp " + (c.client === "claude-code" ? "get liferay-legacy" : "list")));
      } else {
        var command = c.client === "claude-desktop" ? "/usr/local/bin/docker" : "docker";
        var json = "{\n  \"mcpServers\": {\n    \"liferay-legacy\": {\n" +
          (c.client === "copilot" ? "      \"type\": \"stdio\",\n" : "") +
          "      \"command\": \"" + command + "\",\n      \"args\": [\n" + jsonArgs(args, "        ") + "\n      ]\n    }\n  }\n}";
        var where = {
          "claude-desktop": "Quit the Claude desktop app, then add <code>liferay-legacy</code> to <code>mcpServers</code> in <code>claude_desktop_config.json</code> (on macOS in <code>~/Library/Application Support/Claude/</code>, on Windows in <code>%APPDATA%\\Claude\\</code>), and start the app again. <code>command</code> is Docker's full path, from <code>which docker</code> in a terminal. More in <a href=\"claude-desktop.html\">With the Claude Desktop App</a>.",
          "copilot": "Put it in <code>~/.copilot/mcp-config.json</code>, or add <code>liferay-legacy</code> to the <code>mcpServers</code> already there. Visual Studio Code and the Copilot CLI both read it. More in <a href=\"copilot.html\">With GitHub Copilot</a>.",
          "other": "Add it to your assistant's <code>mcpServers</code> setting, and restart the assistant. More in <a href=\"stdio.html\">Other AI Assistants</a>.",
        }[c.client];
        html.push("<h3>1. Add the server</h3>", "<p>" + where + "</p>", block("json", json));
        if (c.client === "copilot") {
          html.push("<p>Pull the image first, so the first start doesn't time out: <code>docker pull " + IMAGE + ":" + c.tag + "</code>.</p>");
        }
        html.push("<h3>2. Check that it connects</h3>");
      }

      if (!c.shared) {
        html.push("<p>Ask the assistant <em>\"What Liferay server are you connected to?\"</em> The answer names your Liferay's release, the connection, the write mode, and which tools work. Then follow <a href=\"checking.html\">Checking the Installation</a>.</p>");
      }

      out.innerHTML = (warnings.length ? '<div class="lmcp-warn">' + warnings.join("<br>") + "</div>" : "") + html.join("\n");
    }

    out.addEventListener("click", function (event) {
      var button = event.target.closest(".lmcp-copy");
      if (!button) return;
      var text = button.parentElement.querySelector("code").textContent;
      var done = function () { button.textContent = "Copied"; setTimeout(function () { button.textContent = "Copy"; }, 1500); };
      if (navigator.clipboard && navigator.clipboard.writeText) {
        navigator.clipboard.writeText(text).then(done, function () { button.textContent = "Select and copy"; });
      } else {
        button.textContent = "Select and copy";
      }
    });

    form.addEventListener("input", build);
    form.addEventListener("change", build);
    build();
  })();
  </script>
</div>

## Related Information

- [Installing](index.html)
- [Settings](../reference/settings.html)
- [Upgrading and Removing](upgrading-and-removing.html)
