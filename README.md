[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="888" alt="Visit QuantNet">](http://quantlet.de/)

## [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **DEDA_RandForest** [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/)

```yaml

Name of QuantLet : DEDA_RandForest

Published in : 'DEDA'

Description : 'Example code for Random Forest, extraction of single trees, feature importance, plotting classification maps and comparing random forest.'

Keywords : 'Random forest, feature importance, classification, decision tree, comparision'

See also : ''

Author : Marius Sterling

Submitted : November 26 2018 by Marius Sterling

Example : 

```

![Picture1](RF_accuracy_number_of_features.png)

![Picture2](RF_accuracy_number_of_samples_per_leaf.png)

![Picture3](RF_accuracy_number_of_trees.png)

![Picture4](RF_contour.png)

![Picture5](RF_contour_prob_class_0.png)

![Picture6](RF_contour_prob_class_1.png)

![Picture7](RF_feature_importance.png)

![Picture8](RF_one_tree_no_0.png)

![Picture9](RF_one_tree_no_1.png)

![Picture10](RF_one_tree_no_2.png)

![Picture11](RF_one_tree_no_3.png)

![Picture12](RF_one_tree_no_4.png)

![Picture13](RF_one_tree_no_5.png)

![Picture14](RF_one_tree_no_6.png)

![Picture15](RF_one_tree_no_7.png)

![Picture16](RF_one_tree_no_8.png)

![Picture17](RF_one_tree_no_9.png)

### IPYNB Code
```ipynb

{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Random Forest"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Loading packages"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/home/ms/anaconda3/lib/python3.7/site-packages/sklearn/ensemble/weight_boosting.py:29: DeprecationWarning: numpy.core.umath_tests is an internal NumPy module and should not be imported. It will be removed in a future NumPy release.\n",
      "  from numpy.core.umath_tests import inner1d\n"
     ]
    }
   ],
   "source": [
    "import pandas as pd\n",
    "from sklearn.model_selection import train_test_split\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "from sklearn.tree import export_graphviz\n",
    "from sklearn.datasets import load_breast_cancer,load_iris,load_wine\n",
    "from sklearn.metrics import confusion_matrix\n",
    "import matplotlib.pyplot as plt\n",
    "import matplotlib.image as mpimg\n",
    "import numpy as np\n",
    "import pydot\n",
    "%matplotlib inline"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Loading data and showing description"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Breast Cancer Wisconsin (Diagnostic) Database\n",
      "=============================================\n",
      "\n",
      "Notes\n",
      "-----\n",
      "Data Set Characteristics:\n",
      "    :Number of Instances: 569\n",
      "\n",
      "    :Number of Attributes: 30 numeric, predictive attributes and the class\n",
      "\n",
      "    :Attribute Information:\n",
      "        - radius (mean of distances from center to points on the perimeter)\n",
      "        - texture (standard deviation of gray-scale values)\n",
      "        - perimeter\n",
      "        - area\n",
      "        - smoothness (local variation in radius lengths)\n",
      "        - compactness (perimeter^2 / area - 1.0)\n",
      "        - concavity (severity of concave portions of the contour)\n",
      "        - concave points (number of concave portions of the contour)\n",
      "        - symmetry \n",
      "        - fractal dimension (\"coastline approximation\" - 1)\n",
      "\n",
      "        The mean, standard error, and \"worst\" or largest (mean of the three\n",
      "        largest values) of these features were computed for each image,\n",
      "        resulting in 30 features.  For instance, field 3 is Mean Radius, field\n",
      "        13 is Radius SE, field 23 is Worst Radius.\n",
      "\n",
      "        - class:\n",
      "                - WDBC-Malignant\n",
      "                - WDBC-Benign\n",
      "\n",
      "    :Summary Statistics:\n",
      "\n",
      "    ===================================== ====== ======\n",
      "                                           Min    Max\n",
      "    ===================================== ====== ======\n",
      "    radius (mean):                        6.981  28.11\n",
      "    texture (mean):                       9.71   39.28\n",
      "    perimeter (mean):                     43.79  188.5\n",
      "    area (mean):                          143.5  2501.0\n",
      "    smoothness (mean):                    0.053  0.163\n",
      "    compactness (mean):                   0.019  0.345\n",
      "    concavity (mean):                     0.0    0.427\n",
      "    concave points (mean):                0.0    0.201\n",
      "    symmetry (mean):                      0.106  0.304\n",
      "    fractal dimension (mean):             0.05   0.097\n",
      "    radius (standard error):              0.112  2.873\n",
      "    texture (standard error):             0.36   4.885\n",
      "    perimeter (standard error):           0.757  21.98\n",
      "    area (standard error):                6.802  542.2\n",
      "    smoothness (standard error):          0.002  0.031\n",
      "    compactness (standard error):         0.002  0.135\n",
      "    concavity (standard error):           0.0    0.396\n",
      "    concave points (standard error):      0.0    0.053\n",
      "    symmetry (standard error):            0.008  0.079\n",
      "    fractal dimension (standard error):   0.001  0.03\n",
      "    radius (worst):                       7.93   36.04\n",
      "    texture (worst):                      12.02  49.54\n",
      "    perimeter (worst):                    50.41  251.2\n",
      "    area (worst):                         185.2  4254.0\n",
      "    smoothness (worst):                   0.071  0.223\n",
      "    compactness (worst):                  0.027  1.058\n",
      "    concavity (worst):                    0.0    1.252\n",
      "    concave points (worst):               0.0    0.291\n",
      "    symmetry (worst):                     0.156  0.664\n",
      "    fractal dimension (worst):            0.055  0.208\n",
      "    ===================================== ====== ======\n",
      "\n",
      "    :Missing Attribute Values: None\n",
      "\n",
      "    :Class Distribution: 212 - Malignant, 357 - Benign\n",
      "\n",
      "    :Creator:  Dr. William H. Wolberg, W. Nick Street, Olvi L. Mangasarian\n",
      "\n",
      "    :Donor: Nick Street\n",
      "\n",
      "    :Date: November, 1995\n",
      "\n",
      "This is a copy of UCI ML Breast Cancer Wisconsin (Diagnostic) datasets.\n",
      "https://goo.gl/U2Uwz2\n",
      "\n",
      "Features are computed from a digitized image of a fine needle\n",
      "aspirate (FNA) of a breast mass.  They describe\n",
      "characteristics of the cell nuclei present in the image.\n",
      "\n",
      "Separating plane described above was obtained using\n",
      "Multisurface Method-Tree (MSM-T) [K. P. Bennett, \"Decision Tree\n",
      "Construction Via Linear Programming.\" Proceedings of the 4th\n",
      "Midwest Artificial Intelligence and Cognitive Science Society,\n",
      "pp. 97-101, 1992], a classification method which uses linear\n",
      "programming to construct a decision tree.  Relevant features\n",
      "were selected using an exhaustive search in the space of 1-4\n",
      "features and 1-3 separating planes.\n",
      "\n",
      "The actual linear program used to obtain the separating plane\n",
      "in the 3-dimensional space is that described in:\n",
      "[K. P. Bennett and O. L. Mangasarian: \"Robust Linear\n",
      "Programming Discrimination of Two Linearly Inseparable Sets\",\n",
      "Optimization Methods and Software 1, 1992, 23-34].\n",
      "\n",
      "This database is also available through the UW CS ftp server:\n",
      "\n",
      "ftp ftp.cs.wisc.edu\n",
      "cd math-prog/cpo-dataset/machine-learn/WDBC/\n",
      "\n",
      "References\n",
      "----------\n",
      "   - W.N. Street, W.H. Wolberg and O.L. Mangasarian. Nuclear feature extraction \n",
      "     for breast tumor diagnosis. IS&T/SPIE 1993 International Symposium on \n",
      "     Electronic Imaging: Science and Technology, volume 1905, pages 861-870,\n",
      "     San Jose, CA, 1993.\n",
      "   - O.L. Mangasarian, W.N. Street and W.H. Wolberg. Breast cancer diagnosis and \n",
      "     prognosis via linear programming. Operations Research, 43(4), pages 570-577, \n",
      "     July-August 1995.\n",
      "   - W.H. Wolberg, W.N. Street, and O.L. Mangasarian. Machine learning techniques\n",
      "     to diagnose breast cancer from fine-needle aspirates. Cancer Letters 77 (1994) \n",
      "     163-171.\n",
      "\n"
     ]
    }
   ],
   "source": [
    "data = load_breast_cancer()\n",
    "df = pd.DataFrame(data.data, columns=data.feature_names)\n",
    "target = pd.Series(data.target)\n",
    "print(data.DESCR)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "The shape of the dataset is  (569, 30)\n"
     ]
    }
   ],
   "source": [
    "print('The shape of the dataset is ',df.shape)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Extracting the feature matrix (X) and the target values (y) in a training and test dataset"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [],
   "source": [
    "X_train, X_test, y_train, y_test = train_test_split(\n",
    "    df, \n",
    "    target,\n",
    "    test_size=0.33, # ratio of data that is used for testing\n",
    "    random_state=42, # setting random seed \n",
    "    stratify = target # Keeps the ratio of the labels in train and test the same as in the initial data\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Declaring and fitting a Random Forest classifier on the feature matrix and target values"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "The train accuracy: 0.9895\n",
      "The test accuracy: 0.9415\n"
     ]
    },
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
       "      <th>malignant</th>\n",
       "      <th>benign</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>malignant</th>\n",
       "      <td>64</td>\n",
       "      <td>6</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>benign</th>\n",
       "      <td>5</td>\n",
       "      <td>113</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "           malignant  benign\n",
       "malignant         64       6\n",
       "benign             5     113"
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "n_feature = len(data.feature_names)\n",
    "rf = RandomForestClassifier(\n",
    "    n_estimators=200, # Number of Trees grown\n",
    "    max_features=min(10,n_feature), # Number of randomly picked features for split \n",
    "    max_depth=3, # Max Number of nested splits\n",
    "    random_state=42, # Seed for random number generator\n",
    "    #oob_score=True, # Out of Bag accuracy score\n",
    "    #bootstrap=True # \n",
    ")\n",
    "rf.fit(X_train,y_train)\n",
    "print('The train accuracy: %.4f'%rf.score(X_train,y_train))\n",
    "print('The test accuracy: %.4f'%rf.score(X_test,y_test))\n",
    "pd.DataFrame(confusion_matrix(y_test, rf.predict(X_test)), index=data.target_names, columns=data.target_names)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Extracting the first 10 trees from the random forest."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "for i in np.arange(10):\n",
    "    tree = rf.estimators_[i]\n",
    "    export_graphviz(\n",
    "        tree, \n",
    "        out_file='tree.dot',\n",
    "        feature_names=list(X_train.columns),\n",
    "        rounded=True,\n",
    "        precision=4,\n",
    "        filled=True\n",
    "    )\n",
    "    (graph,) = pydot.graph_from_dot_file('tree.dot')\n",
    "    graph.set_bgcolor('transparent')\n",
    "    graph.write_png('RF_one_tree_no_%i.png'%i)\n",
    "img = mpimg.imread('RF_one_tree_no_%i.png'%i)\n",
    "plt.imshow(img)\n",
    "plt.show(block=False)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Tree 1:\n",
    "![RF_one_tree_no_0](RF_one_tree_no_0.png)"
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
      "The train accuracy: 0.9580\n",
      "The test accuracy: 0.9096\n"
     ]
    },
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
       "      <th>malignant</th>\n",
       "      <th>benign</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>malignant</th>\n",
       "      <td>61</td>\n",
       "      <td>9</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>benign</th>\n",
       "      <td>8</td>\n",
       "      <td>110</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "           malignant  benign\n",
       "malignant         61       9\n",
       "benign             8     110"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "tree = rf.estimators_[0]\n",
    "print('The train accuracy: %.4f'%tree.score(X_train,y_train))\n",
    "print('The test accuracy: %.4f'%tree.score(X_test,y_test))\n",
    "pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Tree 2:\n",
    "![RF_one_tree_no_0](RF_one_tree_no_1.png)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "The train accuracy: 0.9554\n",
      "The test accuracy: 0.9468\n"
     ]
    },
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
       "      <th>malignant</th>\n",
       "      <th>benign</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>malignant</th>\n",
       "      <td>63</td>\n",
       "      <td>7</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>benign</th>\n",
       "      <td>3</td>\n",
       "      <td>115</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "           malignant  benign\n",
       "malignant         63       7\n",
       "benign             3     115"
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "tree = rf.estimators_[1]\n",
    "print('The train accuracy: %.4f'%tree.score(X_train,y_train))\n",
    "print('The test accuracy: %.4f'%tree.score(X_test,y_test))\n",
    "pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Tree 3:\n",
    "![RF_one_tree_no_0](RF_one_tree_no_2.png)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "The train accuracy: 0.9711\n",
      "The test accuracy: 0.9149\n"
     ]
    },
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
       "      <th>malignant</th>\n",
       "      <th>benign</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>malignant</th>\n",
       "      <td>64</td>\n",
       "      <td>6</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>benign</th>\n",
       "      <td>10</td>\n",
       "      <td>108</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "           malignant  benign\n",
       "malignant         64       6\n",
       "benign            10     108"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "tree = rf.estimators_[2]\n",
    "print('The train accuracy: %.4f'%tree.score(X_train,y_train))\n",
    "print('The test accuracy: %.4f'%tree.score(X_test,y_test))\n",
    "pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Plotting feature importance"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 864x720 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "feature_imp = pd.DataFrame(rf.feature_importances_,index=df.columns,columns=['features']).sort_values('features')\n",
    "ax = feature_imp.plot(kind='barh', figsize=(12, 10), zorder=2)\n",
    "plt.xlabel('Feature Importance')\n",
    "plt.ylabel('Variable')\n",
    "plt.tight_layout()\n",
    "plt.savefig('RF_feature_importance.png', dpi=600, transparent=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Fitting Random Forrest for 2 most important features and plotting decision"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "The train accuracy: 0.9449\n",
      "The test accuracy: 0.9202\n"
     ]
    },
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
       "      <th>malignant</th>\n",
       "      <th>benign</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>malignant</th>\n",
       "      <td>60</td>\n",
       "      <td>10</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>benign</th>\n",
       "      <td>5</td>\n",
       "      <td>113</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "           malignant  benign\n",
       "malignant         60      10\n",
       "benign             5     113"
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "s = 100\n",
    "lab = list(feature_imp.sort_values('features',ascending=False)[:2].index)\n",
    "rf = RandomForestClassifier(\n",
    "    n_estimators=300,\n",
    "    random_state=42,\n",
    "    max_depth=3\n",
    ")\n",
    "rf.fit(X_train[lab],y_train)\n",
    "print('The train accuracy: %.4f'%rf.score(X_train[lab],y_train))\n",
    "print('The test accuracy: %.4f'%rf.score(X_test[lab],y_test))\n",
    "pd.DataFrame(confusion_matrix(y_test, rf.predict(X_test[lab])), index=data.target_names, columns=data.target_names)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Setting mesh grid for the map classification"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [],
   "source": [
    "r = abs(X_train.max() - X_train.min())\n",
    "x_min = X_train.min() - r * 0.1\n",
    "x_max = X_train.max() + r * 0.1\n",
    "r /= s\n",
    "\n",
    "xx, yy = np.meshgrid(\n",
    "    np.arange(x_min[lab[0]], x_max[lab[0]], r[lab[0]]), \n",
    "    np.arange(x_min[lab[1]], x_max[lab[1]], r[lab[1]])\n",
    ")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Setting colors for the plots"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [],
   "source": [
    "n_classes = len(set(data.target))\n",
    "if n_classes == 2:\n",
    "    colors = ['b','r']\n",
    "elif n_classes == 3:\n",
    "    colors = ['b','g','r']\n",
    "else:\n",
    "    import matplotlib.cm as cm\n",
    "    cmap = cm.get_cmap('rainbow', n_classes)    # PiYG\n",
    "    colors = [cmap(i) for i in range(cmap.N)]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure()\n",
    "ax = plt.gca()\n",
    "Z = rf.predict(np.c_[xx.ravel(), yy.ravel()])\n",
    "Z = Z.reshape(xx.shape)\n",
    "ax.contourf(xx, yy, Z, alpha=.4)\n",
    "for b in set(y_train):\n",
    "    idx = y_train == b\n",
    "\n",
    "    ax.scatter(\n",
    "        X_train.loc[idx,lab[0]], X_train.loc[idx,lab[1]], \n",
    "       c=np.array(colors)[b], \n",
    "        s=10,\n",
    "        label='%i train'%b\n",
    "    )\n",
    "    idx2 = y_test == b\n",
    "    ax.scatter(\n",
    "        X_test.loc[list(idx2),lab[0]], X_test.loc[list(idx2),lab[1]],\n",
    "        c=np.array(colors)[b],\n",
    "        marker='x',\n",
    "        s=30,\n",
    "        label='%i test'%b\n",
    "    )\n",
    "ax.set_xlim(xx.min(), xx.max())\n",
    "ax.set_ylim(yy.min(), yy.max())\n",
    "plt.xlabel(lab[0])\n",
    "plt.ylabel(lab[1])\n",
    "plt.legend(\n",
    "    loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',\n",
    "    fancybox=False\n",
    ")\n",
    "plt.tight_layout()\n",
    "plt.savefig('RF_contour.png',dpi=300,transparent=True)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Prediction probabilies for the different classes"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    },
    {
     "data": {
      "image/png": "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TU5KSur+7bffhgF89tlnEb179+6anJzcbejQoZ0yMzNtjz32WEx6enrg2Wef3fm0007r7I3359E8KGNMqpSdNVjsjWCUqgvlh3bvSG1W7VJCnlQuL3/OmNZ57NnXrHTbgYNB/LGyPddduZPrrtxZOlpPyxCpupScnFxQUlLC7t27A+Lj44t+/vnnTc2aNTNr1qwJHjVqVKe1a9euf/zxx3c///zzsT/88MMWgOzsbJu7/d55552o8847L/Ppp5/eV1RURHZ2tm3v3r0BTzzxRNyiRYs2RURElDz44INt/vOf/8Q+99xze1977bXYn376aVNcXFyRN96bJwkqVUROB4yIBAETsZr7lPJXroMNTutzqNqlhKqqj9chMddtH9IZpx5k5txQjLX0+o7UsNLzlK8KoYMhVF0yVvNAQUGB/PWvf23/559/htpsNnbu3Bnsbv/K9hswYMDRm2++uUNhYaHtyiuvPHz66acf+/jjj8O3bt0a0r9//64AhYWFkpKSklMf78uTBHUL8BLQFkgD5gN/82ZQStUld0VeT7T0RVX18V5/v6PbPqQ58+JLk5On51Gqtv78888gu91O27Zti+655574mJiYwlmzZm0vKSkhNDQ0xd1rHn/88Vh3+w0dOjRn0aJFG2fNmhV5ww03dJw4ceL+qKiookGDBmXNnTt3u7tjeZMnfVBdjDGjjTGxxpgYq6pEN28HplRdqclS7lWt53SiQRLVOY9StbFnz56Am266qf24cePSbTYbmZmZ9ri4uEK73c6rr77aqrjY8XMaGRlZnJOTU/oDWtl+mzZtCmrbtm3h3XffnTFmzJiM5cuXNzvnnHOOLl26NGzt2rXB4GgeXL16dTBA8+bNizMzM702SMKTK6j/B/T1YJtSfqn8Uu4vTTmptCRRh8Rclq+O5OixAMZetRNwNO9dfWkaUz4qm9QefbEbD9+5vkwfUrNmxaVf7fYSiott2G0lTJq4kTXrI6u1ZLxSnsjPz7d17do12TnM/Jprrjn4yCOP7Ae444470keMGHHSF1980XLQoEHZoaGhJQD9+/c/FhAQYLp06ZJ87bXXZlS237x588InT57cJiAgwDRr1qx42rRp2+Pj44veeOONHSNHjuxUUFAgAI888sjuU045JX/s2LEZQ4cOTYqJiSlcsmTJprp+r2Iq+fdORAYCpwN3AC+6PBUB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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "for cl in range(n_classes):\n",
    "    plt.figure()\n",
    "    ax = plt.gca()\n",
    "    Z = rf.predict_proba(np.c_[xx.ravel(), yy.ravel()])[:,cl]\n",
    "    Z = Z.reshape(xx.shape)\n",
    "    ax.contourf(xx, yy, Z, alpha=.4)\n",
    "    for b in set(y_train):\n",
    "        idx = y_train == b\n",
    "\n",
    "        ax.scatter(\n",
    "            X_train.loc[idx,lab[0]], X_train.loc[idx,lab[1]], \n",
    "           c=np.array(colors)[b], \n",
    "            s=10,\n",
    "            label='%i train'%b\n",
    "        )\n",
    "        idx2 = y_test == b\n",
    "        ax.scatter(\n",
    "            X_test.loc[list(idx2),lab[0]], X_test.loc[list(idx2),lab[1]],\n",
    "            c=np.array(colors)[b],\n",
    "            marker='x',\n",
    "            s=30,\n",
    "            label='%i test'%b\n",
    "        )\n",
    "    ax.set_xlim(xx.min(), xx.max())\n",
    "    ax.set_ylim(yy.min(), yy.max())\n",
    "    plt.xlabel(lab[0])\n",
    "    plt.ylabel(lab[1])\n",
    "    plt.legend(\n",
    "        loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',\n",
    "        fancybox=False\n",
    "    )\n",
    "    plt.tight_layout()\n",
    "    plt.savefig('RF_contour_prob_class_%i.png'%cl,dpi=300,transparent=True)\n",
    "    plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Comparison"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Accuracy vs. number of trees"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "rf = RandomForestClassifier(\n",
    "    n_estimators=200, # Number of Trees grown\n",
    "    max_features=min(10,n_feature), # Number of randomly picked features for split \n",
    "    max_depth=5, # Max Number of nested splits\n",
    "    random_state=42,\n",
    ")\n",
    "res = []\n",
    "for i in range(1,150,1):\n",
    "    rf.n_estimators = i\n",
    "    rf.fit(X_train,y_train)\n",
    "    d = dict({'n_estimators':i})\n",
    "    d.update({'train':rf.score(X_train,y_train)})\n",
    "    d.update({'test':rf.score(X_test,y_test)})\n",
    "    res.append(d)\n",
    "res = pd.DataFrame(res)\n",
    "res.plot('n_estimators')\n",
    "plt.ylabel('Accuracy')\n",
    "plt.xlabel('Number of trees')\n",
    "plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)\n",
    "plt.savefig('RF_accuracy_number_of_trees.png',dpi=300,transparent=True)\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Accuracy vs. Number of Samples per leaf"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "rf = RandomForestClassifier(\n",
    "    n_estimators=500,\n",
    "    max_features=min(10,n_feature),\n",
    "    min_samples_leaf=1,\n",
    "    random_state=42,\n",
    ")\n",
    "min_sample_class = min([sum(y_train==i) for i in set(y_train)])\n",
    "res = []\n",
    "for i in range(1,min_sample_class):\n",
    "    rf.min_samples_leaf = i\n",
    "    rf.fit(X_train,y_train)\n",
    "    d = dict({'min_samples_leaf':i})\n",
    "    d.update({'train':rf.score(X_train,y_train)})\n",
    "    d.update({'test':rf.score(X_test,y_test)})\n",
    "    res.append(d)\n",
    "res = pd.DataFrame(res)\n",
    "res.plot('min_samples_leaf')\n",
    "plt.ylabel('Accuracy')\n",
    "plt.xlabel('Minimum number of samples in each leaf')\n",
    "plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)\n",
    "plt.savefig('RF_accuracy_number_of_samples_per_leaf.png',dpi=300,transparent=True)\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Accuracy vs. Maximum features"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "rf = RandomForestClassifier(\n",
    "    n_estimators=500,\n",
    "    max_features=min(10,n_feature),\n",
    "    random_state=42,\n",
    "    max_depth=5\n",
    ")\n",
    "res = []\n",
    "for i in range(1,X_train.shape[1]+1):\n",
    "    rf.max_features = i\n",
    "    rf.fit(X_train,y_train)\n",
    "    d = dict({'max_features':i})\n",
    "    d.update({'train':rf.score(X_train,y_train)})\n",
    "    d.update({'test':rf.score(X_test,y_test)})\n",
    "    res.append(d)\n",
    "res = pd.DataFrame(res)\n",
    "res.plot('max_features')\n",
    "plt.ylabel('Accuracy')\n",
    "plt.xlabel('Maximum Number of Features selected')\n",
    "plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)\n",
    "plt.savefig('RF_accuracy_number_of_features.png',dpi=300,transparent=True)\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
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
   "version": "3.7.0"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}

```

automatically created on 2018-12-17

### PYTHON Code
```python

#!/usr/bin/env python
# coding: utf-8

# # Random Forest

# Loading packages

# In[1]:


import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.tree import export_graphviz
from sklearn.datasets import load_breast_cancer,load_iris,load_wine
from sklearn.metrics import confusion_matrix
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import numpy as np
import pydot
get_ipython().run_line_magic('matplotlib', 'inline')


# Loading data and showing description

# In[2]:


data = load_breast_cancer()
df = pd.DataFrame(data.data, columns=data.feature_names)
target = pd.Series(data.target)
print(data.DESCR)


# In[3]:


print('The shape of the dataset is ',df.shape)


# Extracting the feature matrix (X) and the target values (y) in a training and test dataset

# In[4]:


X_train, X_test, y_train, y_test = train_test_split(
    df, 
    target,
    test_size=0.33, # ratio of data that is used for testing
    random_state=42, # setting random seed 
    stratify = target # Keeps the ratio of the labels in train and test the same as in the initial data
)


# Declaring and fitting a Random Forest classifier on the feature matrix and target values

# In[5]:


n_feature = len(data.feature_names)
rf = RandomForestClassifier(
    n_estimators=200, # Number of Trees grown
    max_features=min(10,n_feature), # Number of randomly picked features for split 
    max_depth=3, # Max Number of nested splits
    random_state=42, # Seed for random number generator
    #oob_score=True, # Out of Bag accuracy score
    #bootstrap=True # 
)
rf.fit(X_train,y_train)
print('The train accuracy: %.4f'%rf.score(X_train,y_train))
print('The test accuracy: %.4f'%rf.score(X_test,y_test))
pd.DataFrame(confusion_matrix(y_test, rf.predict(X_test)), index=data.target_names, columns=data.target_names)


# Extracting the first 10 trees from the random forest.

# In[6]:


for i in np.arange(10):
    tree = rf.estimators_[i]
    export_graphviz(
        tree, 
        out_file='tree.dot',
        feature_names=list(X_train.columns),
        rounded=True,
        precision=4,
        filled=True
    )
    (graph,) = pydot.graph_from_dot_file('tree.dot')
    graph.set_bgcolor('transparent')
    graph.write_png('RF_one_tree_no_%i.png'%i)
img = mpimg.imread('RF_one_tree_no_%i.png'%i)
plt.imshow(img)
plt.show(block=False)


# ### Tree 1:
# ![RF_one_tree_no_0](RF_one_tree_no_0.png)

# In[7]:


tree = rf.estimators_[0]
print('The train accuracy: %.4f'%tree.score(X_train,y_train))
print('The test accuracy: %.4f'%tree.score(X_test,y_test))
pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)


# ### Tree 2:
# ![RF_one_tree_no_0](RF_one_tree_no_1.png)

# In[8]:


tree = rf.estimators_[1]
print('The train accuracy: %.4f'%tree.score(X_train,y_train))
print('The test accuracy: %.4f'%tree.score(X_test,y_test))
pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)


# ### Tree 3:
# ![RF_one_tree_no_0](RF_one_tree_no_2.png)

# In[9]:


tree = rf.estimators_[2]
print('The train accuracy: %.4f'%tree.score(X_train,y_train))
print('The test accuracy: %.4f'%tree.score(X_test,y_test))
pd.DataFrame(confusion_matrix(y_test, tree.predict(X_test)), index=data.target_names, columns=data.target_names)


# ### Plotting feature importance

# In[10]:


feature_imp = pd.DataFrame(rf.feature_importances_,index=df.columns,columns=['features']).sort_values('features')
ax = feature_imp.plot(kind='barh', figsize=(12, 10), zorder=2)
plt.xlabel('Feature Importance')
plt.ylabel('Variable')
plt.tight_layout()
plt.savefig('RF_feature_importance.png', dpi=600, transparent=True)


# ### Fitting Random Forrest for 2 most important features and plotting decision

# In[11]:


s = 100
lab = list(feature_imp.sort_values('features',ascending=False)[:2].index)
rf = RandomForestClassifier(
    n_estimators=300,
    random_state=42,
    max_depth=3
)
rf.fit(X_train[lab],y_train)
print('The train accuracy: %.4f'%rf.score(X_train[lab],y_train))
print('The test accuracy: %.4f'%rf.score(X_test[lab],y_test))
pd.DataFrame(confusion_matrix(y_test, rf.predict(X_test[lab])), index=data.target_names, columns=data.target_names)


# Setting mesh grid for the map classification

# In[12]:


r = abs(X_train.max() - X_train.min())
x_min = X_train.min() - r * 0.1
x_max = X_train.max() + r * 0.1
r /= s

xx, yy = np.meshgrid(
    np.arange(x_min[lab[0]], x_max[lab[0]], r[lab[0]]), 
    np.arange(x_min[lab[1]], x_max[lab[1]], r[lab[1]])
)


# Setting colors for the plots

# In[13]:


n_classes = len(set(data.target))
if n_classes == 2:
    colors = ['b','r']
elif n_classes == 3:
    colors = ['b','g','r']
else:
    import matplotlib.cm as cm
    cmap = cm.get_cmap('rainbow', n_classes)    # PiYG
    colors = [cmap(i) for i in range(cmap.N)]


# In[14]:


plt.figure()
ax = plt.gca()
Z = rf.predict(np.c_[xx.ravel(), yy.ravel()])
Z = Z.reshape(xx.shape)
ax.contourf(xx, yy, Z, alpha=.4)
for b in set(y_train):
    idx = y_train == b

    ax.scatter(
        X_train.loc[idx,lab[0]], X_train.loc[idx,lab[1]], 
       c=np.array(colors)[b], 
        s=10,
        label='%i train'%b
    )
    idx2 = y_test == b
    ax.scatter(
        X_test.loc[list(idx2),lab[0]], X_test.loc[list(idx2),lab[1]],
        c=np.array(colors)[b],
        marker='x',
        s=30,
        label='%i test'%b
    )
ax.set_xlim(xx.min(), xx.max())
ax.set_ylim(yy.min(), yy.max())
plt.xlabel(lab[0])
plt.ylabel(lab[1])
plt.legend(
    loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',
    fancybox=False
)
plt.tight_layout()
plt.savefig('RF_contour.png',dpi=300,transparent=True)
plt.show()


# Prediction probabilies for the different classes

# In[15]:


for cl in range(n_classes):
    plt.figure()
    ax = plt.gca()
    Z = rf.predict_proba(np.c_[xx.ravel(), yy.ravel()])[:,cl]
    Z = Z.reshape(xx.shape)
    ax.contourf(xx, yy, Z, alpha=.4)
    for b in set(y_train):
        idx = y_train == b

        ax.scatter(
            X_train.loc[idx,lab[0]], X_train.loc[idx,lab[1]], 
           c=np.array(colors)[b], 
            s=10,
            label='%i train'%b
        )
        idx2 = y_test == b
        ax.scatter(
            X_test.loc[list(idx2),lab[0]], X_test.loc[list(idx2),lab[1]],
            c=np.array(colors)[b],
            marker='x',
            s=30,
            label='%i test'%b
        )
    ax.set_xlim(xx.min(), xx.max())
    ax.set_ylim(yy.min(), yy.max())
    plt.xlabel(lab[0])
    plt.ylabel(lab[1])
    plt.legend(
        loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',
        fancybox=False
    )
    plt.tight_layout()
    plt.savefig('RF_contour_prob_class_%i.png'%cl,dpi=300,transparent=True)
    plt.show()


# ## Comparison

# ### Accuracy vs. number of trees

# In[16]:


rf = RandomForestClassifier(
    n_estimators=200, # Number of Trees grown
    max_features=min(10,n_feature), # Number of randomly picked features for split 
    max_depth=5, # Max Number of nested splits
    random_state=42,
)
res = []
for i in range(1,150,1):
    rf.n_estimators = i
    rf.fit(X_train,y_train)
    d = dict({'n_estimators':i})
    d.update({'train':rf.score(X_train,y_train)})
    d.update({'test':rf.score(X_test,y_test)})
    res.append(d)
res = pd.DataFrame(res)
res.plot('n_estimators')
plt.ylabel('Accuracy')
plt.xlabel('Number of trees')
plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)
plt.savefig('RF_accuracy_number_of_trees.png',dpi=300,transparent=True)
plt.tight_layout()
plt.show()


# ### Accuracy vs. Number of Samples per leaf

# In[17]:


rf = RandomForestClassifier(
    n_estimators=500,
    max_features=min(10,n_feature),
    min_samples_leaf=1,
    random_state=42,
)
min_sample_class = min([sum(y_train==i) for i in set(y_train)])
res = []
for i in range(1,min_sample_class):
    rf.min_samples_leaf = i
    rf.fit(X_train,y_train)
    d = dict({'min_samples_leaf':i})
    d.update({'train':rf.score(X_train,y_train)})
    d.update({'test':rf.score(X_test,y_test)})
    res.append(d)
res = pd.DataFrame(res)
res.plot('min_samples_leaf')
plt.ylabel('Accuracy')
plt.xlabel('Minimum number of samples in each leaf')
plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)
plt.savefig('RF_accuracy_number_of_samples_per_leaf.png',dpi=300,transparent=True)
plt.tight_layout()
plt.show()


# ### Accuracy vs. Maximum features

# In[18]:


rf = RandomForestClassifier(
    n_estimators=500,
    max_features=min(10,n_feature),
    random_state=42,
    max_depth=5
)
res = []
for i in range(1,X_train.shape[1]+1):
    rf.max_features = i
    rf.fit(X_train,y_train)
    d = dict({'max_features':i})
    d.update({'train':rf.score(X_train,y_train)})
    d.update({'test':rf.score(X_test,y_test)})
    res.append(d)
res = pd.DataFrame(res)
res.plot('max_features')
plt.ylabel('Accuracy')
plt.xlabel('Maximum Number of Features selected')
plt.legend(loc='center left',bbox_to_anchor=(1,0.5), title='Dataset',fancybox=False)
plt.savefig('RF_accuracy_number_of_features.png',dpi=300,transparent=True)
plt.tight_layout()
plt.show()


# In[ ]:





```

automatically created on 2018-12-17