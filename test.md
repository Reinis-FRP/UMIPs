* <img src="https://render.githubusercontent.com/render/math?math=X"> denotes the size of a particular transaction someone is seeking to bridge
* <img src="https://render.githubusercontent.com/render/math?math=0 \leq U_t \leq 1"> denotes the utilization of the liquidity providers' capital prior to the transaction, i.e. the amount of the liquidity providers' capital that is in use prior to the current transaction
* <img src="https://render.githubusercontent.com/render/math?math=0 \leq \hat{U}_t \leq 1"> denotes the utilization of the liquidity providers' capital after to the transaction, i.e. the amount of the liquidity providers' capital that would be in use if the user chose to execute their transaction
* <img src="https://render.githubusercontent.com/render/math?math=\bar{U}"> denotes the "kink utilization" where the slope on the interest rate changes
* <img src="https://render.githubusercontent.com/render/math?math=R_0, R_1, R_2"> denotes the parameters governing the interest rate model slopes:
  * <img src="https://render.githubusercontent.com/render/math?math=R_0"> is the interest rate that would be charged at 0% utilization
  * <img src="https://render.githubusercontent.com/render/math?math=R_0 %2b R_1"> is the interest rate that would be charged at <img src="https://render.githubusercontent.com/render/math?math=\bar{U}\%25"> utilization
  * <img src="https://render.githubusercontent.com/render/math?math=R_0 %2b R_1 %2b R_2"> is the interest rate that would be charged at 100% utilization

An interest rate at any given amount of utilization would be given by the following equation:

<img src="https://render.githubusercontent.com/render/math?math=R(U_t) = R_0 %2b \frac{\min(\bar{U}, U_t)}{\bar{U}} R_1 %2b \frac{\max(0, U_t %2d \bar{U})}{1 %2d \bar{U}} R_2">

In our case for a loan, we must integrate over a range of utilizations because each dollar of the loan pushes up the interest rate for the next dollar of the loan. This effect is captured using an integral:

<img src="https://render.githubusercontent.com/render/math?math=R^a_t = \int_{U_t}^{\hat{U}_t} R(u) du">

To get the true rate charged on these loans, we need to de-annualize it to get the percentage:

<img src="https://render.githubusercontent.com/render/math?math=R^w_t = (1 %2b R^a_t)^{\frac{1}{52}} %2d 1">
