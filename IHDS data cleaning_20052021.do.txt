*Code for the book chapter
*Labour, Gender and Work in the Regions of India During the Covid-19 Period (Olsen et al., 2021)
*By Wendy Olsen, Manasi Bera, Clelia Cascella, Amaresh Dubey, Jihye Kim, Zoe Williams, Purva Yadav
*Code by Jihye Kim, Social Statistics, UOM
*Date: 20 May 2021

*IHDS-II data cleaning
*Use IHDS wave2 data
use "\...\data_IHDS_wave2.dta", clear

*Merge with hh dataset
drop _merge
merge m:1 IDHH using "\...\hhindustry.dta"
drop _merge
merge m:1 IDHH using "\...\NF4C.dta"
drop _merge
merge m:1 IDHH using "\...\hhfarm.dta"
drop _merge
merge m:1 IDHH using "\...\hhproduct.dta"

*Daily working hours 
replace	FM38	=0	if	FM38	==.
replace	NF13	=0	if	NF13	==.
replace	NF33	=0	if	NF33	==.
replace	NF53	=0	if	NF53	==.
replace	WS8	    =0	if	WS8 	==.

*Working hours per week
replace	FM38	=16	if	FM38    >16
replace	NF13	=16	if	NF13	>16
replace	NF33	=16	if	NF33	>16
replace	NF53	=16	if	NF53	>16
replace	WS8	= 16	if	WS8	>16
gen dayhours=FM38+ NF13 + NF33+ NF53+ WS8
replace dayhours=16 if dayhours>16
gen NF100=NF13+NF33+NF53
replace NF100=16 if NF100>16
gen Totalwork=dayhours*7

*Relative weight
egen meanWT=mean(WT)
gen fweight=WT/meanWT
gen fwt=round(fweight, 1)
recode fwt 0=1

*Farming hours
gen fmhour=FM38*7 
replace fmhour=0 if fmhour==.
replace fmhour=NF100*7 if fmhour==0 & NF100>0 & (NF1B==61|NF1B==62) 

*Hours of work for informal jobs including farming
gen informalhours=0 
replace informalhours=WS8 if WS8>0 & (WS9==1) 
replace informalhours=informalhours+NF100 if NF100> 0 & (RO7==5|RO7==6) & (NF1B!=61 & NF1B!=62)
replace informalhours=informalhours+FM38 if FM38>0
replace informalhours=NF100 if NF100>0 & (NF1B==61|NF1B==62)
replace informalhours=16 if informalhours>16 
gen informalhours7=informalhours*7 
recode informalhours7 .=0

*Hours of work for formal jobs
gen formalhours=0 
replace formalhours=WS8 if WS8>0 & (WS9==2| WS9==3) 
replace formalhours=formalhours+NF100 if NF100>0 & RO7==7 & (NF1B!=61 & NF1B!=62)
gen formalhours7=formalhours*7 
replace formalhours=16 if formalhours>16 
recode formalhours7 .=0

*Informal vs formal workers
gen informal=0
replace informal=1 if informalhours>0
gen formal=0
replace formal=1 if formalhours>0

*Age groups
gen agegroup=.
replace agegroup=0 if age2>=12 & age2<=14
replace agegroup=1 if age2>=15 & age2<=17
replace agegroup=2 if age2>=18 & age2<=60
replace agegroup=3 if age2>=61 & age2!=.

*Industrial groups by seven categories
label define industry 0 "Farming" 1	"	Agr. labourers" 2	"	Mining & quarrying	" 3	"	Manufacturing	" 4	"	Construction	" 5	"	Wholesale & retail trade	" 6	" Restaurants & hotel" 7	"	Other services	"
gen industry=.
replace   industry=2 if WS5>=10 & WS5<20 & (WS8>=NF100) & WS8>0
replace   industry=3 if WS5>=20 & WS5<39 & (WS8>=NF100) & WS8>0 
replace   industry=4 if (WS5==50| WS5==51) & (WS8>=NF100) & WS8>0
replace   industry=5 if WS5>=60 & WS5<69 & (WS8>=NF100) & WS8>0
replace   industry=6 if WS5==69 & (WS8>=NF100)  & WS8>0
replace   industry=7 if (WS5>69 & WS5<100)|(WS5>=39 & WS5<50) & (WS8>=NF100) & WS8>0 & industry==.
replace   industry=2 if NF1A>=10 & NF1A<20  & (WS8<NF100)  & NF100>0 & industry==.
replace   industry=3 if NF1A>=20 & NF1A<39  & (WS8<NF100) & NF100>0 & industry==.
replace   industry=4 if (NF1A==50| NF1A==51)  & (WS8<NF100) & NF100>0 & industry==.
replace   industry=5 if NF1A>=60 & NF1A<69 & (WS8<NF100) & NF100>0 & industry==.
replace   industry=6 if NF1A==69  & (WS8<NF100) & NF100>0 & industry==.
replace   industry=7 if (NF1A>69 & NF1A<100)|(NF1A>=39 & NF1A<50) & (WS8<NF100) & NF100>0 & industry==.
replace   industry=2 if NF21A>=10 & NF21A<20 & industry==. & (WS8<NF100) & NF100>0
replace   industry=3 if NF21A>=20 & NF21A<39 & industry==. & (WS8<NF100) & NF100>0
replace   industry=4 if (NF21A==50| NF21A==51) & industry==. & (WS8<NF100) & NF100>0
replace   industry=5 if NF21A>=60 & NF21A<69 & industry==. & (WS8<NF100) & NF100>0
replace   industry=6 if NF21A==69 & industry==. & (WS8<NF100) & NF100>0
replace   industry=7 if (NF21A>69 & NF21A<100)|(NF21A>=39 & NF21A<50) & industry==. & (WS8<NF100) & NF100>0
replace   industry=2 if NF41A>=10 & NF41A<20 & industry==. & (WS8<NF100) & NF100>0
replace   industry=3 if NF41A>=20 & NF41A<39 & industry==. & (WS8<NF100) & NF100>0
replace   industry=4 if (NF41A==50| NF41A==51) & industry==. & (WS8<NF100) & NF100>0
replace   industry=5 if NF41A>=60 & NF41A<69 & industry==. & (WS8<NF100) & NF100>0
replace   industry=6 if NF41A==69 & industry==. & (WS8<NF100) & NF100>0
replace   industry=7 if (NF41A>69 & NF41A<100)|(NF41A>=39 & NF41A<50) & industry==. & NF100>0 & (WS8<NF100)
replace   industry=1 if WS5==0 & WS5<10 & (WS8>=NF100) & WS8>0 & industry==.
replace   industry=0 if NF1A==0 & NF1A<10  & (WS8<NF100)  & NF100>0 & industry==.
replace   industry=0 if NF21A==0 & NF21A<10 & (WS8<NF100)  & NF100>0 & industry==. 
replace   industry=0 if NF41A==0 & NF41A<10 & (WS8<NF100)  & NF100>0 & industry==. 
replace   industry=0 if FM38>0 & industry==.

*Industrial groups by three categories
gen industry3=.
replace industry3=0 if (industry==0|industry==1)
replace industry3=1 if (industry==2|industry==4|industry==5|industry==6|industry==7)
replace industry3=2 if (industry==3)
label define industry3 0 "Agriculture" 1 "Services" 2 "Manufacturing" 
label values industry3 industry3

*Labelling
label values industry industry
label drop sex
label define sex 0 "Male" 1 "Female"
label values sex sex
label define agegroup 0 "Ages 12-14" 1 "Ages 15-17" 2 "Ages 18-60" 3 "Ages 61+"
label values agegroup agegroup
label define ruralurban2 0 "Rural" 1 "Urban" 
label values ruralurban2 ruralurban2

*Figure 4 Panel A
gen formal1=formal 
replace formal1=0 if Totalwork<35 & (agegroup==0|agegroup==1)
graph bar (count) formal1 [fweight=fwt] if formal1==1 & agegroup==2, over(industry3, label(labsize(large))) over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large))) by(agegroup) stack ytitle("Count of Formal Employment") ylabel(,angle(horizontal))
graph bar (count) formal1 [fweight=fwt] if formal1==1 & agegroup!=2, over(industry3, label(labsize(large))) over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large))) by(agegroup) stack ytitle("Count of Formal Employment") ylabel(,angle(horizontal))

*Figure 4 Panel B
gen informal1=informal 
replace informal1=0 if Totalwork<35 & (agegroup==0|agegroup==1)
graph bar (count) informal1 [fweight=fwt] if informal1==1 & agegroup==2, over(industry3, label(labsize(large))) over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large))) by(agegroup) stack ytitle("Count of Informal Employment") ylabel(,angle(horizontal))
graph bar (count) informal1 [fweight=fwt] if informal1==1 & agegroup!=2, over(industry3, label(labsize(large))) over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large))) by(agegroup) stack ytitle("Count of Formal Employment") ylabel(,angle(horizontal))

*Table 1
table industry3 sex agegroup [fweight=fwt] if formal1==1 & ruralurban2==0 
table industry3 sex agegroup [fweight=fwt] if formal1==1 & ruralurban2==1
table industry3 sex agegroup [fweight=fwt] if informal1==1 & ruralurban2==0 
table industry3 sex agegroup [fweight=fwt] if informal1==1 & ruralurban2==1
table agegroup sex [fweight=fwt] if ruralurban2==0 
table agegroup sex [fweight=fwt] if ruralurban2==1

*Figure 6
gen migration1=1 if MG11!=.
recode migration1 .=0
by IDHH, sort: egen migration = max(migration1)
graph bar (mean) informal1 [fweight=fwt] if age2>=18 & age2<=60 & migration==0 & MG11==. , over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large))) ytitle("Weighted Mean of Work Status") ylabel(,angle(horizontal))
graph bar (mean) informal1 [fweight=fwt] if age2>=18 & age2<=60 & migration==1 & MG11==. , over(sex, label(labsize(large))) over(ruralurban2, label(labsize(large)))  ytitle("Weighted Mean of Work Status") ylabel(,angle(horizontal))

save "\...\GLU_IHDS.dta", replace
