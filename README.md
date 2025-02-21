# Testing PLATFORMS interactions

Our main repo has problems with the `ruby` platform being removed from `Gemfile.lock` when we update gems.

This repository contains a minimal reproduction case.

## To see the problem:

1. `bundle install` this Gemfile
2. run `bundle update --conservative nokogiri`
3. observe the diff in `Gemfile.lock`

## What we get:

```diff
diff --git a/Gemfile.lock b/Gemfile.lock
index a49faf9..bec207d 100644
--- a/Gemfile.lock
+++ b/Gemfile.lock
@@ -1,15 +1,11 @@
 GEM
   remote: https://rubygems.org/
   specs:
-    mini_portile2 (2.8.8)
-    nokogiri (1.18.2)
-      mini_portile2 (~> 2.8.2)
+    nokogiri (1.18.3-arm64-darwin)
       racc (~> 1.4)
-    nokogiri (1.18.2-arm64-darwin)
+    nokogiri (1.18.3-x86_64-darwin)
       racc (~> 1.4)
-    nokogiri (1.18.2-x86_64-darwin)
-      racc (~> 1.4)
-    nokogiri (1.18.2-x86_64-linux-gnu)
+    nokogiri (1.18.3-x86_64-linux-gnu)
       racc (~> 1.4)
     racc (1.8.1)
     sorbet (0.5.11835)
@@ -19,7 +15,6 @@ GEM
 
 PLATFORMS
   arm64-darwin
-  ruby
   x86_64-darwin
   x86_64-linux
```

## What we want:

```diff
diff --git a/Gemfile.lock b/Gemfile.lock
index a49faf9..bec207d 100644
--- a/Gemfile.lock
+++ b/Gemfile.lock
@@ -1,15 +1,11 @@
 GEM
   remote: https://rubygems.org/
   specs:
     mini_portile2 (2.8.8)
-    nokogiri (1.18.2)
+    nokogiri (1.18.3)
       mini_portile2 (~> 2.8.2)
       racc (~> 1.4)
-    nokogiri (1.18.2-arm64-darwin)
+    nokogiri (1.18.3-arm64-darwin)
       racc (~> 1.4)
-    nokogiri (1.18.2-x86_64-darwin)
+    nokogiri (1.18.3-x86_64-darwin)
       racc (~> 1.4)
-    nokogiri (1.18.2-x86_64-linux-gnu)
+    nokogiri (1.18.3-x86_64-linux-gnu)
       racc (~> 1.4)
     racc (1.8.1)
     sorbet (0.5.11835)
```

## Probable cause

The sorbet-static gem doesn't have a `ruby` platform variant, and yet we were able to `bundle install` it without it complaining or updating `Gemfile.lock`.  Subsequent `bundle update`s seem to resolve that problem by dropping any `ruby` variants and the entry in PLATFORMS.
