---
title: "Minecraft Forge 1.18.2 Part1 自分のMODの下準備"
emoji: "⚙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Java"
  ,"minecraft"
  ,"IntelliJ"
  ,"初心者"
  ,"minecraftmodding"]
published: false
---
# はじめに
こんにちは！
DegeneracyCraft開発者のEpitapです！
MODを作ろうとしてもよく分からない方、いらっしゃるのではないでしょうか？
この記事は(私が)出来る限り思い出して書き連ねるといったものです！
まずは開発環境構築というもの(コードを描くキャンバス+そのコードを実行可能にする環境が整ったパッケージというイメージでOKです)を起動するという解説をしようと思いましたが…

https://qiita.com/tooooowa/items/15ced22071759d7d79a3

この記事が**非常に分かりやすく**書いているのでこれを参照するとよいでしょう！

この次にForgeのMODファイルをダウンロードを行いましょう！

https://files.minecraftforge.net/net/minecraftforge/forge/index_1.18.2.html

Show All Versionから様々なVersionのmdkファイルをダウンロードできます。
ダウンロードしたのち、適当なフォルダを作成して解凍しましょう。

その後IntelliJ IDEAでファイルを開くと勝手に環境を整えてくれるはずです！

:::message
数分から数十分(私の場合は最長30分)かかります。
:::
# いざ設定開始！(周辺環境編)
MODの基本的条件としてこのようにしましょう

:::message
* **ABCMODというMODをつくる**
* **作者はXYZとする**
:::

まずはbulid.gradleというファイルを適したコードに変更していきます。

:::details bulid.gradle
```diff gradle
version = '1.0'
-group = 'com.yourname.modid' 
+group = 'net.xyz.abcmod'
-archivesBaseName = 'modid'
+archivesBaseName = 'abcmod'

 runs {
        client {
            mods {
-                examplemod {
+                abcmod {
                    source sourceSets.main
                }
            }
        }

        server {
            mods {
-                examplemod {
+                abcmod {
                    source sourceSets.main
                }
            }
        }
        
        gameTestServer {
            mods {
-                examplemod {
+                abcmod {
                    source sourceSets.main
                }
            }
        }

        data {
            mods {
-                examplemod {
+                abcmod {
                    source sourceSets.main
                }
            }
        }
    }
}

jar {
    manifest {
        attributes([
-               "Specification-Title"     : "examplemod",
+               "Specification-Title"     : "abcmod",
-               "Specification-Vendor"    : "examplemodsareus",
+               "Specification-Vendor"    : "abcemodsareus",
                "Specification-Version"   : "1", // We are version 1 of ourselves
                "Implementation-Title"    : project.name,
                "Implementation-Version"  : project.jar.archiveVersion,
-               "Implementation-Vendor"   : "examplemodsareus",
+               "Implementation-Vendor"   : "abcmodsareus",
                "Implementation-Timestamp": new Date().format("yyyy-MM-dd'T'HH:mm:ssZ")
        ])
    }
}

```
:::

このように変更した後、画面の右上あたりに🐘○マークがあると思います。これは環境構成を更新する更新ボタンです。それを押して更新しましょう！(もし表示がなかったら、画面右上のGradleタブからグルグルマークボタンを押すと更新できます)
Bulidが終了しましたら、画面下タブのターミナルを開くと

```diff java
PS ...\...\展開したフォルダ名(例:forge-1.18.2-40.2.0-mdk)> 
```
というものがあると思いますので、これについて

```diff java
PS ...\...\展開したフォルダ名(例:forge-1.18.2-40.2.0-mdk)> ./gradlew genIntellijRuns
```
と追加し、コマンドを起動させますと
```diff java
<-------------> 0% CONFIGURING [13s]
```
のようにコマンドの進行度が表示されます。これは実行構成というプログラムを実行させるときに必要な構成を自動で形成してくれています！

```diff java
BUILD SUCCESSFUL in ~~~s
```

という表記が出たらコマンド完了です。
右上の現在のファイルという欄をみるとRunClientを中心とする実行構成が構築されていることが分かります。

**これにて周辺環境の準備完了です！**

:::message
(見やすいように…MOD前提をここにも書いておきます)
* **ABCMODというMODをつくる**
* **作者はXYZとする**
:::
# いざ設定開始！(MODメインファイル編)
次にメインのファイルを変更していきたいと思います！

その中でExampleModのクラスを見るとこのようなものになっています。

:::details Main > Java > com > example > examplemod > ExampleMod (結構長いです)

```diff java:
package com.example.examplemod;

import com.mojang.logging.LogUtils;
import net.minecraft.world.level.block.Block;
import net.minecraft.world.level.block.Blocks;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.event.RegistryEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;
import net.minecraftforge.fml.InterModComms;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.event.lifecycle.FMLCommonSetupEvent;
import net.minecraftforge.fml.event.lifecycle.InterModEnqueueEvent;
import net.minecraftforge.fml.event.lifecycle.InterModProcessEvent;
import net.minecraftforge.event.server.ServerStartingEvent;
import net.minecraftforge.fml.javafmlmod.FMLJavaModLoadingContext;
import org.slf4j.Logger;

import java.util.stream.Collectors;

// The value here should match an entry in the META-INF/mods.toml file
@Mod("examplemod")
public class ExampleMod
{
    // Directly reference a slf4j logger
    private static final Logger LOGGER = LogUtils.getLogger();

    public ExampleMod()
    {
        // Register the setup method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::setup);
        // Register the enqueueIMC method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::enqueueIMC);
        // Register the processIMC method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::processIMC);

        // Register ourselves for server and other game events we are interested in
        MinecraftForge.EVENT_BUS.register(this);
    }

    private void setup(final FMLCommonSetupEvent event)
    {
        // some preinit code
        LOGGER.info("HELLO FROM PREINIT");
        LOGGER.info("DIRT BLOCK >> {}", Blocks.DIRT.getRegistryName());
    }

    private void enqueueIMC(final InterModEnqueueEvent event)
    {
        // Some example code to dispatch IMC to another mod
        InterModComms.sendTo("examplemod", "helloworld", () -> { LOGGER.info("Hello world from the MDK"); return "Hello world";});
    }

    private void processIMC(final InterModProcessEvent event)
    {
        // Some example code to receive and process InterModComms from other mods
        LOGGER.info("Got IMC {}", event.getIMCStream().
                map(m->m.messageSupplier().get()).
                collect(Collectors.toList()));
    }

    // You can use SubscribeEvent and let the Event Bus discover methods to call
    @SubscribeEvent
    public void onServerStarting(ServerStartingEvent event)
    {
        // Do something when the server starts
        LOGGER.info("HELLO from server starting");
    }

    // You can use EventBusSubscriber to automatically subscribe events on the contained class (this is subscribing to the MOD
    // Event bus for receiving Registry Events)
    @Mod.EventBusSubscriber(bus = Mod.EventBusSubscriber.Bus.MOD)
    public static class RegistryEvents
    {
        @SubscribeEvent
        public static void onBlocksRegistry(final RegistryEvent.Register<Block> blockRegistryEvent)
        {
            // Register a new block here
            LOGGER.info("HELLO from Register Block");
        }
    }
}
```
:::

クリックしてみてみたらわかると思いますが、かなり長いですね…これを適した名称、IDにしていきます。
まずはcomのところをnetに名称を変えると分かりやすくなるので、それに変えつつ、また途中のexampleの部分をMOD作者であるxyzとして、ExampleModの部分を自分の作りたいMODとしていきましょう！
:::details Main > Java > net > xyz > abcmod > ABCMod (変更後)

```diff java
package net.xyz.abcmod;

import com.mojang.logging.LogUtils;
import net.minecraft.world.level.block.Block;
import net.minecraft.world.level.block.Blocks;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.event.RegistryEvent;
import net.minecraftforge.eventbus.api.SubscribeEvent;
import net.minecraftforge.fml.InterModComms;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.event.lifecycle.FMLCommonSetupEvent;
import net.minecraftforge.fml.event.lifecycle.InterModEnqueueEvent;
import net.minecraftforge.fml.event.lifecycle.InterModProcessEvent;
import net.minecraftforge.event.server.ServerStartingEvent;
import net.minecraftforge.fml.javafmlmod.FMLJavaModLoadingContext;
import org.slf4j.Logger;

import java.util.stream.Collectors;

// The value here should match an entry in the META-INF/mods.toml file
@Mod(ABCMod.MOD_ID)
public class ABCMod
{
    public static final String MOD_ID = "abcmod";
    // Directly reference a slf4j logger
    private static final Logger LOGGER = LogUtils.getLogger();

    public ABCMod()
    {
        // Register the setup method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::setup);
        // Register the enqueueIMC method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::enqueueIMC);
        // Register the processIMC method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::processIMC);

        // Register ourselves for server and other game events we are interested in
        MinecraftForge.EVENT_BUS.register(this);
    }

    private void setup(final FMLCommonSetupEvent event)
    {
        // some preinit code
        LOGGER.info("HELLO FROM PREINIT");
        LOGGER.info("DIRT BLOCK >> {}", Blocks.DIRT.getRegistryName());
    }

    private void enqueueIMC(final InterModEnqueueEvent event)
    {
        // Some example code to dispatch IMC to another mod
        InterModComms.sendTo("examplemod", "helloworld", () -> { LOGGER.info("Hello world from the MDK"); return "Hello world";});
    }

    private void processIMC(final InterModProcessEvent event)
    {
        // Some example code to receive and process InterModComms from other mods
        LOGGER.info("Got IMC {}", event.getIMCStream().
                map(m->m.messageSupplier().get()).
                collect(Collectors.toList()));
    }

    // You can use SubscribeEvent and let the Event Bus discover methods to call
    @SubscribeEvent
    public void onServerStarting(ServerStartingEvent event)
    {
        // Do something when the server starts
        LOGGER.info("HELLO from server starting");
    }

    // You can use EventBusSubscriber to automatically subscribe events on the contained class (this is subscribing to the MOD
    // Event bus for receiving Registry Events)
    @Mod.EventBusSubscriber(bus = Mod.EventBusSubscriber.Bus.MOD)
    public static class RegistryEvents
    {
        @SubscribeEvent
        public static void onBlocksRegistry(final RegistryEvent.Register<Block> blockRegistryEvent)
        {
            // Register a new block here
            LOGGER.info("HELLO from Register Block");
        }
    }
}


```
:::

これでこのファイルは変更終了…ではありません！これを短縮していきましょう！
ExampleModの中にはいくつかのコードが内蔵されていますが、それらを使わずに別のコードを用いてブロックやアイテムの登録を行っていきます。

:::details Main > Java > net > xyz > abcmod > ABCMod (変更＆短縮後)

```diff java
package net.xyz.abcmod;

import com.mojang.logging.LogUtils;
import net.minecraft.world.level.block.Blocks;
import net.minecraftforge.common.MinecraftForge;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.event.lifecycle.FMLCommonSetupEvent;
import net.minecraftforge.fml.javafmlmod.FMLJavaModLoadingContext;
import org.slf4j.Logger;

// The value here should match an entry in the META-INF/mods.toml file
@Mod(ABCMod.MOD_ID)
public class ABCMod
{
    public static final String MOD_ID = "abcmod";
    // Directly reference a slf4j logger
    private static final Logger LOGGER = LogUtils.getLogger();

    public ABCMod()
    {
        // Register the setup method for modloading
        FMLJavaModLoadingContext.get().getModEventBus().addListener(this::setup);
        // Register ourselves for server and other game events we are interested in
        MinecraftForge.EVENT_BUS.register(this);
    }

    private void setup(final FMLCommonSetupEvent event)
    {
        // some preinit code
        LOGGER.info("HELLO FROM PREINIT");
        LOGGER.info("DIRT BLOCK >> {}", Blocks.DIRT.getRegistryName());
    }
    
}

```
:::
かなり少なくなりましたね！
ただここにいろいろなClassが集結するので、忘れないように…

また、別のファイルでもう一つ書き換えをしなければなりません。
それがmods.tomlです。

:::details Main > resources > META-INF > mods.toml

```diff toml
loaderVersion="[40,)"
-license="All rights reserved"
+license="MIT"
[[mods]] #mandatory
# The modid of the mod
-modId="examplemod" #mandatory
+modId="abcmod" #mandatory
version="${file.jarVersion}" #mandatory
 # A display name for the mod
displayName="ABC MOD" #mandatory
#updateJSONURL="https://change.me.example.invalid/updates.json" #optional
# A URL for the "homepage" for this mod, displayed in the mod UI
#displayURL="https://change.me.to.your.mods.homepage.example.invalid/" #optional
# A file name (in the root of the mod JAR) containing a logo for display
logoFile="examplemod.png" #optional
# A text field displayed in the mod UI
-credits="Thanks for this example mod goes to Java" #optional
+credits="ABCMOD is constructed by Epitap" #optional
# A text field displayed in the mod UI
-authors="Love, Cheese and small house plants" #optional
+authors="XYZ, My name..." #optional
# The description text for the mod (multi line!) (#mandatory)
description='''
-This is a long form description of the mod. You can write whatever you want here
-Have some lorem ipsum.
-Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed mollis lacinia magna. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Sed sagittis luctus odio eu tempus. Interdum et malesuada fames ac ante ipsum primis in faucibus. Pellentesque volutpat ligula eget lacus auctor sagittis. In hac habitasse platea dictumst. Nunc gravida elit vitae sem vehicula efficitur. Donec mattis ipsum et arcu lobortis, eleifend sagittis sem rutrum. Cras pharetra quam eget posuere fermentum. Sed id tincidunt justo. Lorem ipsum dolor sit amet, consectetur adipiscing elit.
+Hello...!
'''
# A dependency - use the . to indicate dependency for a specific modid. Dependencies are optional.
-[[dependencies.examplemod]] #optional
+[[dependencies.abcmod]] #optional
    # the modid of the dependency
    modId="forge" #mandatory
    # Does this dependency have to exist - if not, ordering below must be specified
    mandatory=true #mandatory
    # The version range of the dependency
    versionRange="[40,)" #mandatory
    # An ordering relationship for the dependency - BEFORE or AFTER required if the relationship is not mandatory
    ordering="NONE"
    # Side this dependency is applied on - BOTH, CLIENT or SERVER
    side="BOTH"
# Here's another dependency
-[[dependencies.examplemod]]
+[[dependencies.abcmod]]
    modId="minecraft"
    mandatory=true
# This version range declares a minimum of the current minecraft version up to but not including the next major version
    versionRange="[1.18.2,1.19)"
    ordering="NONE"
    side="BOTH"
```
:::

**これの変更が終了すればいよいよ、起動の時です！**
右上のGradleタブより、Tasks > forgegradle runs > genIntellijRunsを押して起動させましょう。これは数秒で終わるでしょう。

:::message
Gradle > Tasks > forgegradle runs > genIntellijRuns
は何かと更新に使いますので、覚えておいた方がいいでしょう！
:::

画面右上の現在のファイルタブより、RunClientを選択し、▶を押して起動しましょう！

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3721423/06652383-7be8-a394-dabf-e61d338b3703.png)
このような画面が表示されたら成功です！

![](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3721423/cd62766a-d016-5471-87f2-d3d5efcdcaa9.png)

先程mods.tomlで入力した内容がしっかりと明示されています！

# おわりに
以上で最初の準備についての解説を終わります！
個人的にはかなり丁寧に書いたと思います…(大丈夫か結構心配です、一応自分でも構成を組み立てて並行してやっていきましたが…)

次はアイテムをenumに似た形で登録する方法を書いていこうと思います！
長かったと思いますが、ここまで読んでくれてありがとうございました！
もしよければフォローやいいねをよろしくお願いいたします！
