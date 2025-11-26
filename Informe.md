# Tarea 3: Protección de Lectura en XV6

## Guía Paso a Paso

1. Ya se tenía un fork de https://github.com/mit-pdos/xv6-riscv en el repositorio propio.
2. Se creó una rama llamada riscv para la tarea 3.
3. Se clonó el repositorio en el computador.
4. Las dependencias ya estaban instaladas de tareas anteriores (WSL, toolchain RISC-V).
5. Se abrió el repositorio, cd xv6-riscv-tarea3.
6. Se agregaron las declaraciones de funciones en kernel/defs.h:
   - int mrdprotect(uint64 addr, int len);
   - int munrdprotect(uint64 addr, int len);
7. Se implementaron las funciones en kernel/vm.c:
   - mrdprotect: limpia el bit PTE_R de las páginas en el rango.
   - munrdprotect: restaura el bit PTE_R de las páginas en el rango.
8. Se agregaron los números de syscall en kernel/syscall.h:
   - #define SYS_mrdprotect 22
   - #define SYS_munrdprotect 23
9. Se agregaron los wrappers en kernel/sysproc.c:
   - sys_mrdprotect(void)
   - sys_munrdprotect(void)
10. Se registraron las syscalls en kernel/syscall.c:
    - extern uint64 sys_mrdprotect(void);
    - extern uint64 sys_munrdprotect(void);
    - Se agregaron las entradas en el arreglo syscalls[].
11. Se agregaron las interfaces de usuario en user/user.h:
    - int mrdprotect(void *addr, int len);
    - int munrdprotect(void *addr, int len);
12. Se agregaron las entradas en user/usys.pl:
    - entry("mrdprotect");
    - entry("munrdprotect");
13. Se creó el programa de prueba user/protect.c.
14. Se agregó $U/_protect al Makefile en la sección UPROGS.
15. Se intentó compilar con make TOOLPREFIX=riscv64-unknown-elf- qemu, arrojando el error: conflicting types para mrdprotect y munrdprotect.
16. Se eliminaron las declaraciones extern duplicadas en kernel/sysproc.c (líneas 107-108).
17. Se volvió a compilar y apareció el error: passing argument makes integer from pointer.
18. Se eliminó el cast (void*) en kernel/sysproc.c.
19. Se reconstruyó:
    - make clean
    - make TOOLPREFIX=riscv64-unknown-elf- qemu
20. Apareció un error en user/protect.c: passing argument makes pointer from integer.
21. Se eliminó el cast (uint64) en la llamada a mrdprotect y se pasó el puntero directamente.
22. Se compiló nuevamente con make TOOLPREFIX=riscv64-unknown-elf- qemu y funcionó.
23. Se ejecutó el programa protect dentro de xv6.
