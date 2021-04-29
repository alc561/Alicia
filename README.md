# Alicia
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "<a href=\"https://www.bigdatauniversity.com\"><img src = \"https://ibm.box.com/shared/static/cw2c7r3o20w9zn8gkecaeyjhgw3xdgbj.png\" width = 400, align = \"center\"></a>\n",
    "\n",
    "<h1 align=center><font size = 5> Classification with Python</font></h1>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "In this notebook we try to practice all the classification algorithms that we learned in this course.\n",
    "\n",
    "We load a dataset using Pandas library, and apply the following algorithms, and find the best one for this specific dataset by accuracy evaluation methods.\n",
    "\n",
    "Lets first load required libraries:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [],
   "source": [
    "import itertools\n",
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "from matplotlib.ticker import NullFormatter\n",
    "import pandas as pd\n",
    "import numpy as np\n",
    "import matplotlib.ticker as ticker\n",
    "from sklearn import preprocessing\n",
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### About dataset"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "This dataset is about past loans. The __Loan_train.csv__ data set includes details of 346 customers whose loan are already paid off or defaulted. It includes following fields:\n",
    "\n",
    "| Field          | Description                                                                           |\n",
    "|----------------|---------------------------------------------------------------------------------------|\n",
    "| Loan_status    | Whether a loan is paid off on in collection                                           |\n",
    "| Principal      | Basic principal loan amount at the                                                    |\n",
    "| Terms          | Origination terms which can be weekly (7 days), biweekly, and monthly payoff schedule |\n",
    "| Effective_date | When the loan got originated and took effects                                         |\n",
    "| Due_date       | Since it’s one-time payoff schedule, each loan has one single due date                |\n",
    "| Age            | Age of applicant                                                                      |\n",
    "| Education      | Education of applicant                                                                |\n",
    "| Gender         | The gender of applicant                                                               |"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Lets download the dataset"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "--2018-06-12 16:16:44--  https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_train.csv\n",
      "Resolving s3-api.us-geo.objectstorage.softlayer.net... 67.228.254.193\n",
      "Connecting to s3-api.us-geo.objectstorage.softlayer.net|67.228.254.193|:443... connected.\n",
      "HTTP request sent, awaiting response... 200 OK\n",
      "Length: 23101 (23K) [text/csv]\n",
      "Saving to: ‘loan_train.csv’\n",
      "\n",
      "loan_train.csv      100%[===================>]  22.56K  63.3KB/s    in 0.4s    \n",
      "\n",
      "2018-06-12 16:16:45 (63.3 KB/s) - ‘loan_train.csv’ saved [23101/23101]\n",
      "\n"
     ]
    }
   ],
   "source": [
    "!wget -O loan_train.csv https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_train.csv"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Load Data From CSV File  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Unnamed: 0</th>\n",
       "      <th>Unnamed: 0.1</th>\n",
       "      <th>loan_status</th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>effective_date</th>\n",
       "      <th>due_date</th>\n",
       "      <th>age</th>\n",
       "      <th>education</th>\n",
       "      <th>Gender</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/8/2016</td>\n",
       "      <td>10/7/2016</td>\n",
       "      <td>45</td>\n",
       "      <td>High School or Below</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>2</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/8/2016</td>\n",
       "      <td>10/7/2016</td>\n",
       "      <td>33</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>9/8/2016</td>\n",
       "      <td>9/22/2016</td>\n",
       "      <td>27</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>4</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/9/2016</td>\n",
       "      <td>10/8/2016</td>\n",
       "      <td>28</td>\n",
       "      <td>college</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>6</td>\n",
       "      <td>6</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/9/2016</td>\n",
       "      <td>10/8/2016</td>\n",
       "      <td>29</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n",
       "0           0             0     PAIDOFF       1000     30       9/8/2016   \n",
       "1           2             2     PAIDOFF       1000     30       9/8/2016   \n",
       "2           3             3     PAIDOFF       1000     15       9/8/2016   \n",
       "3           4             4     PAIDOFF       1000     30       9/9/2016   \n",
       "4           6             6     PAIDOFF       1000     30       9/9/2016   \n",
       "\n",
       "    due_date  age             education  Gender  \n",
       "0  10/7/2016   45  High School or Below    male  \n",
       "1  10/7/2016   33              Bechalor  female  \n",
       "2  9/22/2016   27               college    male  \n",
       "3  10/8/2016   28               college  female  \n",
       "4  10/8/2016   29               college    male  "
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df = pd.read_csv('loan_train.csv')\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(346, 10)"
      ]
     },
     "execution_count": 4,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.shape"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Convert to date time object "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Unnamed: 0</th>\n",
       "      <th>Unnamed: 0.1</th>\n",
       "      <th>loan_status</th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>effective_date</th>\n",
       "      <th>due_date</th>\n",
       "      <th>age</th>\n",
       "      <th>education</th>\n",
       "      <th>Gender</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>45</td>\n",
       "      <td>High School or Below</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>2</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>33</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-09-22</td>\n",
       "      <td>27</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>4</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>28</td>\n",
       "      <td>college</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>6</td>\n",
       "      <td>6</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>29</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n",
       "0           0             0     PAIDOFF       1000     30     2016-09-08   \n",
       "1           2             2     PAIDOFF       1000     30     2016-09-08   \n",
       "2           3             3     PAIDOFF       1000     15     2016-09-08   \n",
       "3           4             4     PAIDOFF       1000     30     2016-09-09   \n",
       "4           6             6     PAIDOFF       1000     30     2016-09-09   \n",
       "\n",
       "    due_date  age             education  Gender  \n",
       "0 2016-10-07   45  High School or Below    male  \n",
       "1 2016-10-07   33              Bechalor  female  \n",
       "2 2016-09-22   27               college    male  \n",
       "3 2016-10-08   28               college  female  \n",
       "4 2016-10-08   29               college    male  "
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df['due_date'] = pd.to_datetime(df['due_date'])\n",
    "df['effective_date'] = pd.to_datetime(df['effective_date'])\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "# Data visualization and pre-processing\n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Let’s see how many of each class is in our data set "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "PAIDOFF       260\n",
       "COLLECTION     86\n",
       "Name: loan_status, dtype: int64"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df['loan_status'].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "260 people have paid off the loan on time while 86 have gone into collection \n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Lets plot some columns to underestand data better:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Solving environment: done\n",
      "\n",
      "# All requested packages already installed.\n",
      "\n"
     ]
    }
   ],
   "source": [
    "# notice: installing seaborn might takes a few minutes\n",
    "!conda install -c anaconda seaborn -y"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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IIcccgjnnHMODzzwQMt2ffr0aXkkxwUXXMD8+fP53Oc+11L3nDlzGDFiBMOGDePwww9nzpw5LesuvPBCBg4cyMaNGwFYu3YtNTU1HfqdtJcft2FmZW3lO29z1HWPFK2+Z7+Vb+zsrUUEZ555JhMnTmwZtbyhoYHVq1dz4YUXMnXqVMaNG8f69es566yzuPPOO1tGiuiIL3zhCy2jnDfbsGEDp5xyCt/97nc59dRTAXjiiSeorq5uGX5p9OjRTJkypWW8vvnz57fs/+KLL3LFFVcwb948amtreeONNzjxxBM54IADGDFiBJA8W2r69OlccsklHY65I3wFZWZWZE888QRVVVVcfPHFLWV1dXUsX76cY489lnHjxgGwyy67cPvtt3PTTTcV7dj33XcfRx99dEtyAvjsZz/b8kDEtkyZMoVrr72W2tpaAGpra7nmmmu49dZbW7aZPHkyt912G5s3by5a3Pk4QZmZFdnSpUs/8UgMyP+ojCFDhvDhhx/ywQcfdPg4ud12dXV1fPTRR60eu73a8ziPwYMHc9xxx/HjH/+408dpD3fxmZl1k20fi5GrtfLtydfFV6h8MeYru/baaznttNM45ZRTinr8XL6CMjMrskMPPZRFixblLd92XMXXX3+dPn360Ldv3y49dkf23zbGfI/zOPDAA6mrq+PBBx/s9LHa4gRlZlZkY8aMYePGjdx9990tZc8//zxDhw5lwYIF/PrXvwaSBxtefvnlXHnllUU79he/+EV+85vf8POf/7yl7JFHHuGll15q1/5XXHEF3/72t2lsbASgsbGRG2+8ka9//euf2PYb3/gGU6ZMKUrc+biLz8zK2oCB+7XrzruO1NcWScyePZvJkydz00030bt3b2pqavje977HQw89xFe/+lUuvfRStmzZwvnnn7/VreUzZszY6rbuZ55JngE7YsQIdtghuaY455xzGDFiBA888MBWz5O68847OeaYY5g7dy6TJ09m8uTJVFVVMWLECL7//e+36/XV1dVx8803c+qpp7Jp0yaqqqq45ZZbWp4QnOvQQw9l1KhR7X50fUe163EbXc2PE7Du4MdtVAY/biM7Cn3cRptdfJKmS3pP0tKcslslvSppiaTZknZPy2skfSSpIZ1+2N5AzMzMcrXnM6gZwLbXx/OAwyJiBLAcuCZn3e8joi6dLsbMzKwT2kxQEfEk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\n",
      "text/plain": [
       "<Figure size 432x216 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "import seaborn as sns\n",
    "\n",
    "bins = np.linspace(df.Principal.min(), df.Principal.max(), 10)\n",
    "g = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\n",
    "g.map(plt.hist, 'Principal', bins=bins, ec=\"k\")\n",
    "\n",
    "g.axes[-1].legend()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "image/png": "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ZMCAAaxbtw6AlStXMnjw4Hb9TQrhufgKMKi6mg+XLSt2GGbWQUs//ojDb3g6tfpe/tfxbW4TEZxxxhlMmDChZdbyhoYGPv30Uy688ELuu+8+xo0bx5o1azjzzDO59957W2aKaI/vfOc7LbOcN1u7di0nnXQSd9xxB6eccgoAs2bNoqqqqmX6pdGjR3P77be3zNc3e/bslve//vrrXHXVVcycOZPa2lo++OADTjjhBPbZZx+GDx8O5O4t9eCDD3LppZe2O+ZCOUEVIK17yfguuGY9x6xZs6ioqOCSSy5pKaurq+OBBx5g1KhRjBs3DoAddtiBu+++m9GjR3coQeXzyCOPcOSRR7YkJ4Djjjuu4PfffvvtXH/99dTW1gJQW1vLddddx2233cavf/1rACZNmsSdd97J9773vVRizsddfGZmXWDhwoWb3RID8t8qY99992X16tV88cUX7d5P6267uro6vvrqqy3uu1CF3M5j4MCBHH300S0Jqyv4DMrMrBtteluM1rZUvjX5uvg6K1+M+cquv/56Tj31VE466aRU99/MZ1BmZl3goIMOYt68eXnL586du1HZ+++/T58+fejbt2+X7rs97980xny389hvv/2oq6vj8ccf7/C+tsYJysysC4wZM4Z169bxy1/+sqXs1VdfZciQIcyZM4dnn30WyN3Y8IorruDqq69Obd/f/e53+fOf/8zvf//7lrKnn36aN954o6D3X3XVVfz0pz+lsbERgMbGRm666SauvPLKzbb90Y9+xO23355K3JtyF5+Zlb3qAXsXNPKuPfW1RRLTpk1j0qRJ3HzzzVRWVjJ48GDuuusunnzySS6//HImTpzIhg0bOP/88zcaWj5lypSNhnW/9NJLAAwfPpxttsmdV5xzzjkMHz6cxx57bKP7Sd17770cddRRzJgxg0mTJjFp0iQqKioYPnw4P/vZzwo6vrq6Om655RZOOeUUvv76ayoqKrj11ltb7hDc2kEHHcTIkSMLvnV9exR0u4201NfXx6anjaUgrdtk1HzS5NttlL8O3W6jtVJtJ1ni221kR3fcbsPMzKxbOUGZmVkmOUGZWVlyF3jxdfZv4ARlZmWnsrKSVatWOUkVUUSwatUqKisrO1yHR/GZWdmpqamhqamJFStWFDuUHq2yspKamo4PDHOCKlHb0bFfneczsH9/lixdmkpdZllQ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\n",
      "text/plain": [
       "<Figure size 432x216 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "bins=np.linspace(df.age.min(), df.age.max(), 10)\n",
    "g = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\n",
    "g.map(plt.hist, 'age', bins=bins, ec=\"k\")\n",
    "\n",
    "g.axes[-1].legend()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "# Pre-processing:  Feature selection/extraction"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Lets look at the day of the week people get the loan "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x216 with 2 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "df['dayofweek'] = df['effective_date'].dt.dayofweek\n",
    "bins=np.linspace(df.dayofweek.min(), df.dayofweek.max(), 10)\n",
    "g = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\n",
    "g.map(plt.hist, 'dayofweek', bins=bins, ec=\"k\")\n",
    "g.axes[-1].legend()\n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "We see that people who get the loan at the end of the week dont pay it off, so lets use Feature binarization to set a threshold values less then day 4 "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Unnamed: 0</th>\n",
       "      <th>Unnamed: 0.1</th>\n",
       "      <th>loan_status</th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>effective_date</th>\n",
       "      <th>due_date</th>\n",
       "      <th>age</th>\n",
       "      <th>education</th>\n",
       "      <th>Gender</th>\n",
       "      <th>dayofweek</th>\n",
       "      <th>weekend</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>45</td>\n",
       "      <td>High School or Below</td>\n",
       "      <td>male</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>2</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>33</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>female</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-09-22</td>\n",
       "      <td>27</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>4</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>28</td>\n",
       "      <td>college</td>\n",
       "      <td>female</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>6</td>\n",
       "      <td>6</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>29</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n",
       "0           0             0     PAIDOFF       1000     30     2016-09-08   \n",
       "1           2             2     PAIDOFF       1000     30     2016-09-08   \n",
       "2           3             3     PAIDOFF       1000     15     2016-09-08   \n",
       "3           4             4     PAIDOFF       1000     30     2016-09-09   \n",
       "4           6             6     PAIDOFF       1000     30     2016-09-09   \n",
       "\n",
       "    due_date  age             education  Gender  dayofweek  weekend  \n",
       "0 2016-10-07   45  High School or Below    male          3        0  \n",
       "1 2016-10-07   33              Bechalor  female          3        0  \n",
       "2 2016-09-22   27               college    male          3        0  \n",
       "3 2016-10-08   28               college  female          4        1  \n",
       "4 2016-10-08   29               college    male          4        1  "
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df['weekend']= df['dayofweek'].apply(lambda x: 1 if (x>3)  else 0)\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "## Convert Categorical features to numerical values"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Lets look at gender:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Gender  loan_status\n",
       "female  PAIDOFF        0.865385\n",
       "        COLLECTION     0.134615\n",
       "male    PAIDOFF        0.731293\n",
       "        COLLECTION     0.268707\n",
       "Name: loan_status, dtype: float64"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.groupby(['Gender'])['loan_status'].value_counts(normalize=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "86 % of female pay there loans while only 73 % of males pay there loan\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Lets convert male to 0 and female to 1:\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Unnamed: 0</th>\n",
       "      <th>Unnamed: 0.1</th>\n",
       "      <th>loan_status</th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>effective_date</th>\n",
       "      <th>due_date</th>\n",
       "      <th>age</th>\n",
       "      <th>education</th>\n",
       "      <th>Gender</th>\n",
       "      <th>dayofweek</th>\n",
       "      <th>weekend</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>45</td>\n",
       "      <td>High School or Below</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>2</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-10-07</td>\n",
       "      <td>33</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>2016-09-08</td>\n",
       "      <td>2016-09-22</td>\n",
       "      <td>27</td>\n",
       "      <td>college</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>4</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>28</td>\n",
       "      <td>college</td>\n",
       "      <td>1</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>6</td>\n",
       "      <td>6</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>2016-09-09</td>\n",
       "      <td>2016-10-08</td>\n",
       "      <td>29</td>\n",
       "      <td>college</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n",
       "0           0             0     PAIDOFF       1000     30     2016-09-08   \n",
       "1           2             2     PAIDOFF       1000     30     2016-09-08   \n",
       "2           3             3     PAIDOFF       1000     15     2016-09-08   \n",
       "3           4             4     PAIDOFF       1000     30     2016-09-09   \n",
       "4           6             6     PAIDOFF       1000     30     2016-09-09   \n",
       "\n",
       "    due_date  age             education  Gender  dayofweek  weekend  \n",
       "0 2016-10-07   45  High School or Below       0          3        0  \n",
       "1 2016-10-07   33              Bechalor       1          3        0  \n",
       "2 2016-09-22   27               college       0          3        0  \n",
       "3 2016-10-08   28               college       1          4        1  \n",
       "4 2016-10-08   29               college       0          4        1  "
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df['Gender'].replace(to_replace=['male','female'], value=[0,1],inplace=True)\n",
    "df.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "## One Hot Encoding  \n",
    "#### How about education?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "education             loan_status\n",
       "Bechalor              PAIDOFF        0.750000\n",
       "                      COLLECTION     0.250000\n",
       "High School or Below  PAIDOFF        0.741722\n",
       "                      COLLECTION     0.258278\n",
       "Master or Above       COLLECTION     0.500000\n",
       "                      PAIDOFF        0.500000\n",
       "college               PAIDOFF        0.765101\n",
       "                      COLLECTION     0.234899\n",
       "Name: loan_status, dtype: float64"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df.groupby(['education'])['loan_status'].value_counts(normalize=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "#### Feature befor One Hot Encoding"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>age</th>\n",
       "      <th>Gender</th>\n",
       "      <th>education</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>45</td>\n",
       "      <td>0</td>\n",
       "      <td>High School or Below</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>33</td>\n",
       "      <td>1</td>\n",
       "      <td>Bechalor</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>27</td>\n",
       "      <td>0</td>\n",
       "      <td>college</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>28</td>\n",
       "      <td>1</td>\n",
       "      <td>college</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>29</td>\n",
       "      <td>0</td>\n",
       "      <td>college</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Principal  terms  age  Gender             education\n",
       "0       1000     30   45       0  High School or Below\n",
       "1       1000     30   33       1              Bechalor\n",
       "2       1000     15   27       0               college\n",
       "3       1000     30   28       1               college\n",
       "4       1000     30   29       0               college"
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df[['Principal','terms','age','Gender','education']].head()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "#### Use one hot encoding technique to conver categorical varables to binary variables and append them to the feature Data Frame "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>age</th>\n",
       "      <th>Gender</th>\n",
       "      <th>weekend</th>\n",
       "      <th>Bechalor</th>\n",
       "      <th>High School or Below</th>\n",
       "      <th>college</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>45</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>33</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>27</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>28</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>29</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Principal  terms  age  Gender  weekend  Bechalor  High School or Below  \\\n",
       "0       1000     30   45       0        0         0                     1   \n",
       "1       1000     30   33       1        0         1                     0   \n",
       "2       1000     15   27       0        0         0                     0   \n",
       "3       1000     30   28       1        1         0                     0   \n",
       "4       1000     30   29       0        1         0                     0   \n",
       "\n",
       "   college  \n",
       "0        0  \n",
       "1        0  \n",
       "2        1  \n",
       "3        1  \n",
       "4        1  "
      ]
     },
     "execution_count": 16,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "Feature = df[['Principal','terms','age','Gender','weekend']]\n",
    "Feature = pd.concat([Feature,pd.get_dummies(df['education'])], axis=1)\n",
    "Feature.drop(['Master or Above'], axis = 1,inplace=True)\n",
    "Feature.head()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Feature selection"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Lets defind feature sets, X:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>age</th>\n",
       "      <th>Gender</th>\n",
       "      <th>weekend</th>\n",
       "      <th>Bechalor</th>\n",
       "      <th>High School or Below</th>\n",
       "      <th>college</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>45</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>33</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1000</td>\n",
       "      <td>15</td>\n",
       "      <td>27</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>28</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>29</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Principal  terms  age  Gender  weekend  Bechalor  High School or Below  \\\n",
       "0       1000     30   45       0        0         0                     1   \n",
       "1       1000     30   33       1        0         1                     0   \n",
       "2       1000     15   27       0        0         0                     0   \n",
       "3       1000     30   28       1        1         0                     0   \n",
       "4       1000     30   29       0        1         0                     0   \n",
       "\n",
       "   college  \n",
       "0        0  \n",
       "1        0  \n",
       "2        1  \n",
       "3        1  \n",
       "4        1  "
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "X = Feature\n",
    "X[0:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "What are our lables?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "y = df['loan_status'].values\n",
    "y[0:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "## Normalize Data "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Data Standardization give data zero mean and unit variance (technically should be done after train test split )"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[ 0.51578458,  0.92071769,  2.33152555, -0.42056004, -1.20577805,\n",
       "        -0.38170062,  1.13639374, -0.86968108],\n",
       "       [ 0.51578458,  0.92071769,  0.34170148,  2.37778177, -1.20577805,\n",
       "         2.61985426, -0.87997669, -0.86968108],\n",
       "       [ 0.51578458, -0.95911111, -0.65321055, -0.42056004, -1.20577805,\n",
       "        -0.38170062, -0.87997669,  1.14984679],\n",
       "       [ 0.51578458,  0.92071769, -0.48739188,  2.37778177,  0.82934003,\n",
       "        -0.38170062, -0.87997669,  1.14984679],\n",
       "       [ 0.51578458,  0.92071769, -0.3215732 , -0.42056004,  0.82934003,\n",
       "        -0.38170062, -0.87997669,  1.14984679]])"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "X = preprocessing.StandardScaler().fit(X).transform(X)\n",
    "X[0:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "# Classification "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "Now, it is your turn, use the training set to build an accurate model. Then use the test set to report the accuracy of the model\n",
    "You should use the following algorithm:\n",
    "- K Nearest Neighbor(KNN)\n",
    "- Decision Tree\n",
    "- Support Vector Machine\n",
    "- Logistic Regression\n",
    "\n",
    "\n",
    "\n",
    "__ Notice:__ \n",
    "- You can go above and change the pre-processing, feature selection, feature-extraction, and so on, to make a better model.\n",
    "- You should use either scikit-learn, Scipy or Numpy libraries for developing the classification algorithms.\n",
    "- You should include the code of the algorithm in the following cells."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# K Nearest Neighbor(KNN)\n",
    "Notice: You should find the best k to build the model with the best accuracy.  \n",
    "**warning:** You should not use the __loan_test.csv__ for finding the best k, however, you can split your train_loan.csv into train and test to find the best __k__."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Train set: (276, 8) (276,)\n",
      "Test set: (70, 8) (70,)\n"
     ]
    }
   ],
   "source": [
    "# We split the X into train and test to find the best k\n",
    "from sklearn.model_selection import train_test_split\n",
    "X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=4)\n",
    "print ('Train set:', X_train.shape,  y_train.shape)\n",
    "print ('Test set:', X_test.shape,  y_test.shape)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 45,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',\n",
       "           metric_params=None, n_jobs=1, n_neighbors=3, p=2,\n",
       "           weights='uniform')"
      ]
     },
     "execution_count": 45,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Modeling\n",
    "from sklearn.neighbors import KNeighborsClassifier\n",
    "k = 3\n",
    "#Train Model and Predict  \n",
    "kNN_model = KNeighborsClassifier(n_neighbors=k).fit(X_train,y_train)\n",
    "kNN_model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 46,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 46,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# just for sanity chaeck\n",
    "yhat = kNN_model.predict(X_test)\n",
    "yhat[0:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 67,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([ 0.67142857,  0.65714286,  0.71428571,  0.68571429,  0.75714286,\n",
       "        0.71428571,  0.78571429,  0.75714286,  0.75714286,  0.67142857,\n",
       "        0.7       ,  0.72857143,  0.7       ,  0.7       ])"
      ]
     },
     "execution_count": 67,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Best k\n",
    "Ks=15\n",
    "mean_acc=np.zeros((Ks-1))\n",
    "std_acc=np.zeros((Ks-1))\n",
    "ConfustionMx=[];\n",
    "for n in range(1,Ks):\n",
    "    \n",
    "    #Train Model and Predict  \n",
    "    kNN_model = KNeighborsClassifier(n_neighbors=n).fit(X_train,y_train)\n",
    "    yhat = kNN_model.predict(X_test)\n",
    "    \n",
    "    \n",
    "    mean_acc[n-1]=np.mean(yhat==y_test);\n",
    "    \n",
    "    std_acc[n-1]=np.std(yhat==y_test)/np.sqrt(yhat.shape[0])\n",
    "mean_acc"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 68,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',\n",
       "           metric_params=None, n_jobs=1, n_neighbors=7, p=2,\n",
       "           weights='uniform')"
      ]
     },
     "execution_count": 68,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Building the model again, using k=7\n",
    "from sklearn.neighbors import KNeighborsClassifier\n",
    "k = 7\n",
    "#Train Model and Predict  \n",
    "kNN_model = KNeighborsClassifier(n_neighbors=k).fit(X_train,y_train)\n",
    "kNN_model"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Decision Tree"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 84,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=4,\n",
       "            max_features=None, max_leaf_nodes=None,\n",
       "            min_impurity_decrease=0.0, min_impurity_split=None,\n",
       "            min_samples_leaf=1, min_samples_split=2,\n",
       "            min_weight_fraction_leaf=0.0, presort=False, random_state=None,\n",
       "            splitter='best')"
      ]
     },
     "execution_count": 84,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.tree import DecisionTreeClassifier\n",
    "DT_model = DecisionTreeClassifier(criterion=\"entropy\", max_depth = 4)\n",
    "DT_model.fit(X_train,y_train)\n",
    "DT_model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 85,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['COLLECTION', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'COLLECTION', 'COLLECTION', 'PAIDOFF', 'COLLECTION',\n",
       "       'COLLECTION', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'COLLECTION',\n",
       "       'COLLECTION', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'COLLECTION',\n",
       "       'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'COLLECTION',\n",
       "       'COLLECTION', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'COLLECTION',\n",
       "       'PAIDOFF', 'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 85,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "yhat = DT_model.predict(X_test)\n",
    "yhat"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Support Vector Machine"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 77,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,\n",
       "  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',\n",
       "  max_iter=-1, probability=False, random_state=None, shrinking=True,\n",
       "  tol=0.001, verbose=False)"
      ]
     },
     "execution_count": 77,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn import svm\n",
    "SVM_model = svm.SVC()\n",
    "SVM_model.fit(X_train, y_train) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 82,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION',\n",
       "       'COLLECTION', 'PAIDOFF', 'COLLECTION', 'COLLECTION', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 82,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "yhat = SVM_model.predict(X_test)\n",
    "yhat"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Logistic Regression"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 80,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "LogisticRegression(C=0.01, class_weight=None, dual=False, fit_intercept=True,\n",
       "          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,\n",
       "          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,\n",
       "          verbose=0, warm_start=False)"
      ]
     },
     "execution_count": 80,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.linear_model import LogisticRegression\n",
    "LR_model = LogisticRegression(C=0.01).fit(X_train,y_train)\n",
    "LR_model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 81,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'COLLECTION',\n",
       "       'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'COLLECTION', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'COLLECTION', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'COLLECTION', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF',\n",
       "       'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 81,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "yhat = LR_model.predict(X_test)\n",
    "yhat"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Model Evaluation using Test set"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 93,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.metrics import jaccard_similarity_score\n",
    "from sklearn.metrics import f1_score\n",
    "from sklearn.metrics import log_loss"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "First, download and load the test set:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 94,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "--2018-06-12 17:42:41--  https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_test.csv\n",
      "Resolving s3-api.us-geo.objectstorage.softlayer.net... 67.228.254.193\n",
      "Connecting to s3-api.us-geo.objectstorage.softlayer.net|67.228.254.193|:443... connected.\n",
      "HTTP request sent, awaiting response... 200 OK\n",
      "Length: 3642 (3.6K) [text/csv]\n",
      "Saving to: ‘loan_test.csv’\n",
      "\n",
      "loan_test.csv       100%[===================>]   3.56K  --.-KB/s    in 0s      \n",
      "\n",
      "2018-06-12 17:42:42 (105 MB/s) - ‘loan_test.csv’ saved [3642/3642]\n",
      "\n"
     ]
    }
   ],
   "source": [
    "!wget -O loan_test.csv https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_test.csv"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Load Test set for evaluation "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 100,
   "metadata": {
    "button": false,
    "collapsed": true,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Unnamed: 0</th>\n",
       "      <th>Unnamed: 0.1</th>\n",
       "      <th>loan_status</th>\n",
       "      <th>Principal</th>\n",
       "      <th>terms</th>\n",
       "      <th>effective_date</th>\n",
       "      <th>due_date</th>\n",
       "      <th>age</th>\n",
       "      <th>education</th>\n",
       "      <th>Gender</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/8/2016</td>\n",
       "      <td>10/7/2016</td>\n",
       "      <td>50</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>5</td>\n",
       "      <td>5</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>300</td>\n",
       "      <td>7</td>\n",
       "      <td>9/9/2016</td>\n",
       "      <td>9/15/2016</td>\n",
       "      <td>35</td>\n",
       "      <td>Master or Above</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>21</td>\n",
       "      <td>21</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/10/2016</td>\n",
       "      <td>10/9/2016</td>\n",
       "      <td>43</td>\n",
       "      <td>High School or Below</td>\n",
       "      <td>female</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>24</td>\n",
       "      <td>24</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>1000</td>\n",
       "      <td>30</td>\n",
       "      <td>9/10/2016</td>\n",
       "      <td>10/9/2016</td>\n",
       "      <td>26</td>\n",
       "      <td>college</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>35</td>\n",
       "      <td>35</td>\n",
       "      <td>PAIDOFF</td>\n",
       "      <td>800</td>\n",
       "      <td>15</td>\n",
       "      <td>9/11/2016</td>\n",
       "      <td>9/25/2016</td>\n",
       "      <td>29</td>\n",
       "      <td>Bechalor</td>\n",
       "      <td>male</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n",
       "0           1             1     PAIDOFF       1000     30       9/8/2016   \n",
       "1           5             5     PAIDOFF        300      7       9/9/2016   \n",
       "2          21            21     PAIDOFF       1000     30      9/10/2016   \n",
       "3          24            24     PAIDOFF       1000     30      9/10/2016   \n",
       "4          35            35     PAIDOFF        800     15      9/11/2016   \n",
       "\n",
       "    due_date  age             education  Gender  \n",
       "0  10/7/2016   50              Bechalor  female  \n",
       "1  9/15/2016   35       Master or Above    male  \n",
       "2  10/9/2016   43  High School or Below  female  \n",
       "3  10/9/2016   26               college    male  \n",
       "4  9/25/2016   29              Bechalor    male  "
      ]
     },
     "execution_count": 100,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "test_df = pd.read_csv('loan_test.csv')\n",
    "test_df.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 101,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[ 0.49362588,  0.92844966,  3.05981865,  1.97714211, -1.30384048,\n",
       "         2.39791576, -0.79772404, -0.86135677],\n",
       "       [-3.56269116, -1.70427745,  0.53336288, -0.50578054,  0.76696499,\n",
       "        -0.41702883, -0.79772404, -0.86135677],\n",
       "       [ 0.49362588,  0.92844966,  1.88080596,  1.97714211,  0.76696499,\n",
       "        -0.41702883,  1.25356634, -0.86135677],\n",
       "       [ 0.49362588,  0.92844966, -0.98251057, -0.50578054,  0.76696499,\n",
       "        -0.41702883, -0.79772404,  1.16095912],\n",
       "       [-0.66532184, -0.78854628, -0.47721942, -0.50578054,  0.76696499,\n",
       "         2.39791576, -0.79772404, -0.86135677]])"
      ]
     },
     "execution_count": 101,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "## Preprocessing\n",
    "test_df['due_date'] = pd.to_datetime(test_df['due_date'])\n",
    "test_df['effective_date'] = pd.to_datetime(test_df['effective_date'])\n",
    "test_df['dayofweek'] = test_df['effective_date'].dt.dayofweek\n",
    "test_df['weekend'] = test_df['dayofweek'].apply(lambda x: 1 if (x>3)  else 0)\n",
    "test_df['Gender'].replace(to_replace=['male','female'], value=[0,1],inplace=True)\n",
    "test_Feature = test_df[['Principal','terms','age','Gender','weekend']]\n",
    "test_Feature = pd.concat([test_Feature,pd.get_dummies(test_df['education'])], axis=1)\n",
    "test_Feature.drop(['Master or Above'], axis = 1,inplace=True)\n",
    "test_X = preprocessing.StandardScaler().fit(test_Feature).transform(test_Feature)\n",
    "test_X[0:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 102,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF', 'PAIDOFF'], dtype=object)"
      ]
     },
     "execution_count": 102,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "test_y = test_df['loan_status'].values\n",
    "test_y[0:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 103,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "KNN Jaccard index: 0.67\n",
      "KNN F1-score: 0.63\n"
     ]
    }
   ],
   "source": [
    "knn_yhat = kNN_model.predict(test_X)\n",
    "print(\"KNN Jaccard index: %.2f\" % jaccard_similarity_score(test_y, knn_yhat))\n",
    "print(\"KNN F1-score: %.2f\" % f1_score(test_y, knn_yhat, average='weighted') )"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 104,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "DT Jaccard index: 0.72\n",
      "DT F1-score: 0.74\n"
     ]
    }
   ],
   "source": [
    "DT_yhat = DT_model.predict(test_X)\n",
    "print(\"DT Jaccard index: %.2f\" % jaccard_similarity_score(test_y, DT_yhat))\n",
    "print(\"DT F1-score: %.2f\" % f1_score(test_y, DT_yhat, average='weighted') )"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 105,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "SVM Jaccard index: 0.80\n",
      "SVM F1-score: 0.76\n"
     ]
    }
   ],
   "source": [
    "SVM_yhat = SVM_model.predict(test_X)\n",
    "print(\"SVM Jaccard index: %.2f\" % jaccard_similarity_score(test_y, SVM_yhat))\n",
    "print(\"SVM F1-score: %.2f\" % f1_score(test_y, SVM_yhat, average='weighted') )"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 106,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "LR Jaccard index: 0.74\n",
      "LR F1-score: 0.66\n",
      "LR LogLoss: 0.57\n"
     ]
    }
   ],
   "source": [
    "LR_yhat = LR_model.predict(test_X)\n",
    "LR_yhat_prob = LR_model.predict_proba(test_X)\n",
    "print(\"LR Jaccard index: %.2f\" % jaccard_similarity_score(test_y, LR_yhat))\n",
    "print(\"LR F1-score: %.2f\" % f1_score(test_y, LR_yhat, average='weighted') )\n",
    "print(\"LR LogLoss: %.2f\" % log_loss(test_y, LR_yhat_prob))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Report\n",
    "You should be able to report the accuracy of the built model using different evaluation metrics:"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "| Algorithm          | Jaccard | F1-score | LogLoss |\n",
    "|--------------------|---------|----------|---------|\n",
    "| KNN                | 0.67    | 0.63     | NA      |\n",
    "| Decision Tree      | 0.72    | 0.74     | NA      |\n",
    "| SVM                | 0.80    | 0.76     | NA      |\n",
    "| LogisticRegression | 0.74    | 0.66     | 0.57    |"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "## Want to learn more?\n",
    "\n",
    "IBM SPSS Modeler is a comprehensive analytics platform that has many machine learning algorithms. It has been designed to bring predictive intelligence to decisions made by individuals, by groups, by systems – by your enterprise as a whole. A free trial is available through this course, available here: [SPSS Modeler](http://cocl.us/ML0101EN-SPSSModeler).\n",
    "\n",
    "Also, you can use Watson Studio to run these notebooks faster with bigger datasets. Watson Studio is IBM's leading cloud solution for data scientists, built by data scientists. With Jupyter notebooks, RStudio, Apache Spark and popular libraries pre-packaged in the cloud, Watson Studio enables data scientists to collaborate on their projects without having to install anything. Join the fast-growing community of Watson Studio users today with a free account at [Watson Studio](https://cocl.us/ML0101EN_DSX)\n",
    "\n",
    "\n",
    "<hr>\n",
    "Copyright &copy; 2018 [Cognitive Class](https://cocl.us/DX0108EN_CC). This notebook and its source code are released under the terms of the [MIT License](https://bigdatauniversity.com/mit-license/).​"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "button": false,
    "new_sheet": false,
    "run_control": {
     "read_only": false
    }
   },
   "source": [
    "### Thanks for completing this lesson!\n",
    "\n",
    "Notebook created by: <a href = \"https://ca.linkedin.com/in/saeedaghabozorgi\">Saeed Aghabozorgi</a>"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3.5",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.5.5"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
