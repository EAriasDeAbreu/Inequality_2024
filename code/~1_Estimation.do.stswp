********************************************************************************
********************************************************************************
*Authors: Edmundo Arias De Abreu
*Coder: Edmundo Arias De Abreu
*Project: Inequality
*Data: Inequality Data + Factor Shares (Davila, Fernandez and Zuleta)
*Stage: Event Study Estimation

*Last checked: 25.07.2024

/*
********************************************************************************
*                                 Contents                                     *
********************************************************************************


********************************************************************************
*/

*Prepare the terminal
clear
cls

*Set graph format
set scheme s2mono
grstyle init
grstyle set plain, horizontal box
grstyle color background white
grstyle set color navy gs5 gs10
grstyle set color gs10: major_grid
grstyle set lp solid dash dot 
grstyle set symbol circle triangle diamond X
grstyle set legend 6, nobox
graph set window fontface "Garamond"

********************************************************************************
*                                                                              *
*                         0.    Data Loading & Prep                            *
*                                                                              *
********************************************************************************

// Load Inequality Data
import excel "/Users/edmundoarias/Documents/GitHub/Inequality_2024/data/clean/inequality_data.xlsx", sheet("Sheet1") firstrow

duplicates list ctr_id t_year

// Merge 1:1 w/ Factor Shares based on ctr_id and t_year 
merge 1:1 ctr_id t_year using "/Users/edmundoarias/Library/CloudStorage/Dropbox/EdmundoArias RA Work/Fertility/1.:_Data/Boom Data + birth rates + pension constribuions.dta"
drop _merge


// Sort unit-year
sort ctr_id t_year

// Declare panel
xtset  ctr_id t_year, yearly

********************************************************************************
*                                                                              *
*                      1.   Estimation - Discretization #3                     *
*                                                                              *
********************************************************************************
	

************ For Human K Share
	
sort t_year

* Generate the quartile variable for each year
gen ctr_hshare_q = .

* Loop through each year to create quartiles
levelsof t_year, local(years)
foreach year of local years {
    xtile temp_q = ctr_hshare if t_year == `year', nq(4)
    replace ctr_hshare_q = temp_q if t_year == `year'
    drop temp_q
}

sort ctr_id t_year	// Treatment has no within-country time variation
	



by ctr_id: gen ctr_gdp95 = ctr_gdp if t_year == 1995
by ctr_id: replace ctr_gdp95 = ctr_gdp95[_n-1] if missing(ctr_gdp95)
label variable ctr_gdp95 "Country GDP in 1995 (baseline)"
summarize ctr_gdp95
tabulate t_year if ctr_gdp95 == ctr_gdp


********************************************************************************
*                                                                              *
*                      2.       Effect of H-Share                              *
*                                                                              *
********************************************************************************

* Set up controls

// Define the list of control variables
local controls ctr_manufva ctr_pop ctr_servicesva ctr_tradeglobal ctr_emp ctr_gdppc

// Generate baseline values for all control variables at 1995
foreach var in `controls' {
    gen `var'_95 = `var' if t_year == 1995
    bysort ctr_id: egen `var'95_cons = max(`var'_95)
    drop `var'_95
    ren `var'95_cons `var'95
}

// Initialize the controls list
local control_vars ""

// Loop through the control variables, adding one at a time
foreach var in `controls' {
    // Generate the interaction term
    gen `var'95TT = `var'95 * t_year
   
}


*****************              Income Inequality                **************** 


** No Controls
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_hshare_q, ///
        weight() ///
        effects(5) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Human K Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)5)) ///
		trends_nonparam(ctr_incomegroup)
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/H_expto_Income.pdf", replace
		

** Controls: ctr_manufva95TT ctr_pop95TT
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_hshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(5) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Human K Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)5)) ///
		trends_nonparam(ctr_incomegroup)
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/H_expto_Income_controls.pdf", replace


*****************              Wealth Inequality                **************** 

** No Controls
did_multiplegt_dyn ctr_top10wealth ctr_id t_year ctr_hshare_q, ///
        weight() ///
        effects(5) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Human K Share Exposure on Wealth Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)5))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/H_expto_Wealth.pdf", replace

** Controls: ctr_manufva95TT ctr_pop95TT
did_multiplegt_dyn ctr_top10wealth ctr_id t_year ctr_hshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(5) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Human K Share Exposure on Wealth Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)5)) ///
		trends_nonparam(ctr_incomegroup)
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/H_expto_Wealth_controls.pdf", replace
		

		
********************************************************************************
*                                                                              *
*                      3.       Effect of L-Share                              *
*                                                                              *
********************************************************************************

sort t_year

* Generate the quartile variable for each year
gen ctr_zshare_q = .

* Loop through each year to create quartiles
levelsof t_year, local(years)
foreach year of local years {
    xtile temp_q = ctr_zshare if t_year == `year', nq(4)
    replace ctr_zshare_q = temp_q if t_year == `year'
    drop temp_q
}

sort ctr_id t_year	


*****************              Income Inequality                **************** 

** No Controls
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_zshare_q, ///
        weight() ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Labor Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/L_expto_Income.pdf", replace


** Controls: ctr_manufva95TT ctr_pop95TT
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_hshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Labor Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/L_expto_Income_controls.pdf", replace

*****************              Wealth Inequality                **************** 

** No Controls
did_multiplegt_dyn ctr_top10wealth ctr_id t_year ctr_zshare_q, ///
        weight() ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Labor Share Exposure on Wealth Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/L_expto_Wealth.pdf", replace

** Controls: ctr_manufva95TT ctr_pop95TT
did_multiplegt_dyn ctr_top10wealth ctr_id t_year ctr_hshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(6) placebo(5) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Labor Share Exposure on Wealth Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-5(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/L_expto_Wealth_controls.pdf", replace

		
********************************************************************************
*                                                                              *
*                      4.       Effect of z:k Share                            *
*                                                                              *
********************************************************************************

sort t_year

* Generate the quartile variable for each year
gen ctr_zkshare_q = .

* Loop through each year to create quartiles
levelsof t_year, local(years)
foreach year of local years {
    xtile temp_q = ctr_zkshare if t_year == `year', nq(4)
    replace ctr_zkshare_q = temp_q if t_year == `year'
    drop temp_q
}

sort ctr_id t_year	



*****************              Income Inequality                **************** 

** No Controls
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_zkshare_q, ///
        weight() ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Total Labor to Physical Capital Ratio Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/zK_expto_Income.pdf", replace

** Controls: ctr_manufva95TT ctr_pop95TT (!)
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_zkshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Total Labor to Physical Capital Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/zK_expto_Income_controls.pdf", replace

********************************************************************************
*                                                                              *
*                      5.       Effect of K Share                              *
*                                                                              *
********************************************************************************

sort t_year

* Generate the quartile variable for each year
gen ctr_kshare_q = .

* Loop through each year to create quartiles
levelsof t_year, local(years)
foreach year of local years {
    xtile temp_q = ctr_kshare if t_year == `year', nq(4)
    replace ctr_kshare_q = temp_q if t_year == `year'
    drop temp_q
}

sort ctr_id t_year	


*****************              Income Inequality                **************** 

** No Controls (!)
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_kshare_q, ///
        weight() ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Physical Capital Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/K_expto_Income.pdf", replace


** Controls: ctr_manufva95TT ctr_pop95TT (!)
did_multiplegt_dyn ctr_top10income ctr_id t_year ctr_kshare_q, ///
        weight() controls(ctr_manufva95TT ctr_pop95TT) ///
        effects(6) placebo(3) cluster(ctr_id) ci_level(95) ///
        graphoptions(title(Physical Capital Share Exposure on Income Inequality) ///
        ylabel(, nogrid) xline(0, lstyle(dash)) yline(0, lstyle(dash)) ///
        legend(off) xtitle(Relative time to treatment) xlabel(-3(1)6))
graph export "/Users/edmundoarias/Documents/GitHub/Inequality_2024/output/event_study/K_expto_Income_controls.pdf", replace


