## Test case for slow suggest

Apply to tenderjit:01e6292 (only project in the workspace). Slow also with fully annotated project (return void for all methods).

```diff
diff --git i/lib/tenderjit/iseq_compiler.rb w/lib/tenderjit/iseq_compiler.rb
index 66e9ad9..e8d5896 100644
--- i/lib/tenderjit/iseq_compiler.rb
+++ w/lib/tenderjit/iseq_compiler.rb
@@ -2605,6 +2605,17 @@ def handle_getblockparamproxy idx, level
                 # so this function call should already be aligned
                 rt.rb_funcall_without_alignment self, :compile_getblockparamproxy, [REG_CFP, req, rt.return_value]
                 rt.NUM2INT(rt.return_value)
+# To reproduce (there may be a smaller test case):
+#
+# - F12 on return_value -> Esc                   # Not necessary!
+# - F12 on jump -> Esc                           # Necessary
+# - highlight and delete `jump rt.return_value``
+# - Ctrl+Enter
+#
+# It will now take a while.
+#
+rt = Runtime.new
+rt.jump rt.return_value
 
                 rt.jump rt.return_value
               end
```

## Trigger Suggest (Ctrl+Space on instance)

Processed by the server side (in the most minimal form) as:

```
[INFO] Server received textDocument/completion
[INFO] Sending response to textDocument/completion
[INFO] Server received completionItem/resolve
[INFO] Sending response to completionItem/resolve
```

## Go to Definition

Processed as:

```
[INFO] Server received textDocument/didOpen
[INFO] Indexing workspace files in /home/saverio/code/fisk-dev
[INFO] Indexing workspace files in /home/saverio/.rvm/gems/ruby-3.0.2/gems/solargraph-0.44.2
[INFO] Server received textDocument/foldingRange
[INFO] Sending response to textDocument/foldingRange
```

however, the above seems the case where the actual instance type is not detected (two options are proposed).
