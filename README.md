# Powershell oraz active directory

### Skrypt do tworzenia urzytkownikow z pliku txt w active directory:
plik users.txt to:
```
 Tomasz Nowak
 Anna Kowalska
```

```powershell
$file = get-content .\users.txt
$file | % { 
 $pass = -join((65..90) + (97..122) | Get-Random -Count 8 | %{[char]$_}) +"2#"
 $login = ($_.split(' '))[1]
 Write-Host $login $pass
 New-ADUser $login -AccountPassword (ConvertTo-SecureString $pass -AsPlainText -Force)
}
```
### Pierwsza część zaliczenia
1 Zatrzymaj wszystkie procesy, których nazwa zaczyna się na literę p.
```bash
$ps -e | grep " p" | awk '{ print $1 }' | xargs kill
```
```powershell
Get-Process | ?{$_.Name -match "^p.*$"} | % {$_.kill()}
get-process p* | % {$_.kill()}
```
2 Znajdź wszytkie procesy, które zajmują więcej niż 1000MB pamięci i ubij je.
```bash
$ps -el | awk '{ if ( $6 > (1024*10)) { print $3 } }' | grep -v PID | xargs kill
```
```powershell
get-process | select {$_.privatememorysize/1mb -ge 100} | % {$_.kill()}
```
3 Sprawdź ile bajtów zajmują pliki w danej kartotece.
```bash
$tot=0; for file in $( ls )
do
 set -- $( ls -log $file )
 echo $3
 (( tot = $tot + $3 ))
done; echo $tot

$ls -l | awk { tot += $5; print tot; } | tail -1
```
```powershell
$a = Get-ChildItem $directory -recurse | Measure-Object -property length -sum
$a.sum
```
4 Sprawdź czy ilość pamięci potrzebna procesom się nie zmieniła.
```bash
$while [ true ]
 do
 msize1=$(ps -el|grep application|grep -v grep|awk '{print $6}')
 sleep 10
 msize2=$(ps -el|grep application|grep -v grep|awk '{print $6}')
 expr $msize2 - $msize1
 msize1=$msize2
done
```
```powershell
$Output1=0
$Output2=0
$Processes = (get-wmiobject Win32_PerfFormattedData_PerfProc_Process) 
foreach($Process in $Processes)
{
 $Output1 += $Process.PageFileBytes 
}

$Processes = (get-wmiobject Win32_PerfFormattedData_PerfProc_Process) 
foreach($Process in $Processes)
{
 $Output2 += $Process.PageFileBytes 
}

$output1/1mb
$output2/1mb

$output1 -eq $output2
```
5 Sprawdź czy dany proces jeszcze działa.
```bash
$processToWatch=$( ps -e | grep application | awk '{ print $1 }'
$while [ true ]
do
 sleep 10
 processToCheck=$(ps -e |grep application |awk '{print $1}' )
 if [ -z "$processToCheck" -or "$processToWatch" != "$processToCheck" ]
  then
   echo "Process application is not running"
  return
  fi
done
```
```powershell
$procSTART = get-process | select-object id,name
While ((Compare-Object $procSTART (get-process | select-object id,name)) -eq $null)
{
	 sleep 2
	 write-host "true"
} 
Compare-Object $procSTART (get-process | select-object id,name)
```
6 Zamień na duże litery.
```bash
$echo "this is a string" | tr [:lower:] [:upper:]
$echo "this is a string" | tr '[a-z]' '[A.Z]'
```
```powershell
$napis = "ala ma kota"
$napis = $napis.ToUpper()
```
7 Wstaw słowo ABC po drugiej literce w stringu.
```bash
$echo "string" | sed "s|\(.\)\(.*)|\1ABC\2|"
```
```powershell
$str = "string"
$strMOD = ($str.Substring(0,1))+"ABS"+($str.Substring(1,($str.length -1)))
```
8 Odczytaj Bloga z adresu "http://blogs.msdn.com/powershell/rss.aspx"
```powershell
$strona = New-Object Net.WebClient
$strona.DownloadString("http://blogs.msdn.com/powershell/rss.aspx")
```
```powershell
$oIE = new-object -com internetexplorer.application
$oIE.navigate2("http://blogs.msdn.com/powershell/rss.aspx")
$oIE.visible = $true
```
