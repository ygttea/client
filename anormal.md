# Teyyapgui Plugin

Spigot 1.8 plugin. Sunucudan client'a GUI açtırır.

---

## plugin.yml

```yaml
name: TeyyapguiPlugin
version: 1.0
main: xyz.ninrtail.test.Main
api-version: 1.8
commands:
  guitest:
    description: GUI test komutu
    usage: /guitest
```

---

## Main.java

```java
package xyz.ninrtail.test;

import org.bukkit.plugin.java.JavaPlugin;

public class Main extends JavaPlugin {

    @Override
    public void onEnable() {
        // Sunucu -> Client (biz gönderiyoruz)
        this.getServer().getMessenger().registerOutgoingPluginChannel(this, "Teyyapgui");
        // Client -> Sunucu (client REGISTER gönderdiğinde Spigot bunu tanısın diye)
        this.getServer().getMessenger().registerIncomingPluginChannel(this, "Teyyapgui", (channel, player, message) -> {});
        getCommand("guitest").setExecutor(new Gui(this));
        getLogger().info("TeyyapguiPlugin aktif!");
    }

    @Override
    public void onDisable() {
        this.getServer().getMessenger().unregisterOutgoingPluginChannel(this, "Teyyapgui");
        this.getServer().getMessenger().unregisterIncomingPluginChannel(this, "Teyyapgui");
    }
}
```

---

## Gui.java

```java
package xyz.ninrtail.test;

import com.google.gson.JsonObject;
import org.bukkit.command.Command;
import org.bukkit.command.CommandExecutor;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;

import java.nio.charset.StandardCharsets;

public class Gui implements CommandExecutor {

    private final Main plugin;

    public Gui(Main plugin) {
        this.plugin = plugin;
    }

    @Override
    public boolean onCommand(CommandSender sender, Command cmd, String label, String[] args) {
        if (!(sender instanceof Player)) return true;
        Player player = (Player) sender;

        // Gösterilecek içeriği buradan özelleştir
        String title   = "Lapis Dünyası";
        String message = "Bu dünyada lapis kazarak gelir elde edebilirsin. Kazandığın lapislerden anahtar yapabilirsin.";
        String button  = "Evet";

        JsonObject json = new JsonObject();
        json.addProperty("title", title);
        json.addProperty("msg",   message);
        json.addProperty("btn",   button);

        String payload = "openinfo###" + json.toString();
        player.sendPluginMessage(plugin, "Teyyapgui", payload.getBytes(StandardCharsets.UTF_8));

        return true;
    }
}
```

---

## Nasıl kullanılır?

1. `Main.java` ve `Gui.java` dosyalarını `src/main/java/xyz/ninrtail/test/` altına koy.
2. `plugin.yml` dosyasını `src/main/resources/` altına koy.
3. `pom.xml` veya `build.gradle` ile Spigot 1.8 bağımlılığını ekle, jar'ı derle.
4. Jar'ı sunucunun `plugins/` klasörüne at, sunucuyu başlat.
5. Oyunda `/guitest` komutunu çalıştır — client'ta GUI açılır.

---

## Farklı oyunculara farklı içerik göndermek

`Gui.java` içindeki `title`, `message`, `button` değişkenlerini istediğin gibi değiştir.  
Örneğin args'tan okuyabilirsin:

```java
if (args.length >= 3) {
    title   = args[0];
    message = args[1];
    button  = args[2];
}
```

---

## Bağımlılık (pom.xml)

```xml
<repositories>
    <repository>
        <id>spigot-repo</id>
        <url>https://hub.spigotmc.org/nexus/content/repositories/snapshots/</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.spigotmc</groupId>
        <artifactId>spigot-api</artifactId>
        <version>1.8.8-R0.1-SNAPSHOT</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.9</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```
