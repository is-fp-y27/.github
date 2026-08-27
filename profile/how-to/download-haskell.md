# Haskell

## GHCup
Чтобы начать работать с языком, вам потребуется скачать необходимое окружение. На текущий момент, самый простой способ это сделать - использовать [GHCup](https://www.haskell.org/ghcup/). Вам понадобятся следующие компоненты: `GHC`, `GHCi`, `Cabal`, `Haskel Language Server`, `Stack` (опционально)

### Windows:

```
Set-ExecutionPolicy Bypass -Scope Process -Force;[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; try { & ([ScriptBlock]::Create((Invoke-WebRequest https://www.haskell.org/ghcup/sh/bootstrap-haskell.ps1 -UseBasicParsing))) -Interactive -DisableCurl } catch { Write-Error $_ }
```

### Linux, macOS:
```
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
