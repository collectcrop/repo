# hooks  

---

## free_hook  
exit函数中调用了 __rtld_lock_lock_recursive 和 __rtld_lock_unlock_recursive，只要在这两个指针处写入地址（如one_gadget），就能劫持程序的控制流  
这两个函数在_rtld_global结构体里面  

64位下：  

**在libc-2.23中**    

**exit_hook = libc_base+0x5f0040+3848**  

**exit_hook = libc_base+0x5f0040+3856**  

**在libc-2.27中**  

**exit_hook = libc_base+0x619060+3840**  

**exit_hook = libc_base+0x619060+3848**  

**在libc-2.31中**  

**exit_hook = libc_base+0x222060+3848**  

**exit_hook = libc_base+0x222060+3856**  


