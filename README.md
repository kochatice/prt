## Mustafa Ray Portofolio

You can use the [editor on GitHub](https://github.com/kochatice/tett/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Homework 3"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Part 1"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import matplotlib.pyplot as plt\n",
    "%matplotlib inline\n",
    "plt.rcParams[\"figure.dpi\"] = 300\n",
    "plt.rcParams['savefig.bbox'] = 'tight'"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.svm import SVC\n",
    "from sklearn.datasets import load_breast_cancer\n",
    "from sklearn.model_selection import train_test_split\n",
    "from sklearn.preprocessing import MinMaxScaler"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [],
   "source": [
    "# load and split the data\n",
    "cancer = load_breast_cancer()\n",
    "\n",
    "X_train, X_test, y_train, y_test = train_test_split(\n",
    "cancer.data, cancer.target, random_state=0)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Test score: 0.95\n"
     ]
    }
   ],
   "source": [
    "# compute minimum and maximum on the training data\n",
    "scaler = MinMaxScaler().fit(X_train)\n",
    "\n",
    "# rescale the training data\n",
    "X_train_scaled = scaler.transform(X_train)\n",
    "\n",
    "svm = SVC(gamma = 'auto' )\n",
    "\n",
    "# learn an SVM on the scaled training data\n",
    "svm.fit(X_train_scaled, y_train)\n",
    "\n",
    "# scale the test data and score the scaled data\n",
    "X_test_scaled = scaler.transform(X_test)\n",
    "print(\"Test score: {:.2f}\".format(svm.score(X_test_scaled, y_test)))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "2- Building Pipelines"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.pipeline import Pipeline\n",
    "pipe = Pipeline([(\"scaler\", MinMaxScaler()), (\"svm\", SVC(gamma = 'auto'))])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Pipeline(memory=None,\n",
       "     steps=[('scaler', MinMaxScaler(copy=True, feature_range=(0, 1))), ('svm', SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,\n",
       "  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',\n",
       "  max_iter=-1, probability=False, random_state=None, shrinking=True,\n",
       "  tol=0.001, verbose=False))])"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "pipe.fit(X_train, y_train)"
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
      "Test score: 0.95\n"
     ]
    }
   ],
   "source": [
    "print(\"Test score: {:.2f}\".format(pipe.score(X_test, y_test)))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "3- Using Pipelines in Grid Searches (SVM)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.model_selection import GridSearchCV\n",
    "\n",
    "param_grid = {'svm__C': [0.001, 0.01, 0.1, 1, 10, 100],\n",
    "'svm__gamma': [0.001, 0.01, 0.1, 1, 10, 100]}"
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
      "Best cross-validation accuracy: 0.98\n",
      "Test set score: 0.97\n",
      "Best parameters: {'svm__C': 1, 'svm__gamma': 1}\n"
     ]
    }
   ],
   "source": [
    "grid = GridSearchCV(pipe, param_grid=param_grid, cv=5)\n",
    "grid.fit(X_train, y_train)\n",
    "print(\"Best cross-validation accuracy: {:.2f}\".format(grid.best_score_))\n",
    "print(\"Test set score: {:.2f}\".format(grid.score(X_test, y_test)))\n",
    "print(\"Best parameters: {}\".format(grid.best_params_))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "4- Using Pipelines in Grid Searches (Logistic Regression)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.linear_model import LogisticRegression\n",
    "from sklearn.preprocessing import StandardScaler\n",
    "from sklearn.pipeline import make_pipeline"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [],
   "source": [
    "pipe = make_pipeline(StandardScaler(), LogisticRegression(solver ='lbfgs', max_iter=300))\n",
    "param_grid = {'logisticregression__C': [0.01, 0.1, 1, 10, 100]}"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "GridSearchCV(cv=5, error_score='raise-deprecating',\n",
       "       estimator=Pipeline(memory=None,\n",
       "     steps=[('standardscaler', StandardScaler(copy=True, with_mean=True, with_std=True)), ('logisticregression', LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,\n",
       "          intercept_scaling=1, max_iter=300, multi_class='warn',\n",
       "          n_jobs=None, penalty='l2', random_state=None, solver='lbfgs',\n",
       "          tol=0.0001, verbose=0, warm_start=False))]),\n",
       "       fit_params=None, iid='warn', n_jobs=None,\n",
       "       param_grid={'logisticregression__C': [0.01, 0.1, 1, 10, 100]},\n",
       "       pre_dispatch='2*n_jobs', refit=True, return_train_score='warn',\n",
       "       scoring=None, verbose=0)"
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "X_train, X_test, y_train, y_test = train_test_split(\n",
    "cancer.data, cancer.target, random_state=4)\n",
    "grid = GridSearchCV(pipe, param_grid, cv=5)\n",
    "grid.fit(X_train, y_train)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Best estimator:\n",
      "Pipeline(memory=None,\n",
      "     steps=[('standardscaler', StandardScaler(copy=True, with_mean=True, with_std=True)), ('logisticregression', LogisticRegression(C=0.1, class_weight=None, dual=False, fit_intercept=True,\n",
      "          intercept_scaling=1, max_iter=300, multi_class='warn',\n",
      "          n_jobs=None, penalty='l2', random_state=None, solver='lbfgs',\n",
      "          tol=0.0001, verbose=0, warm_start=False))])\n"
     ]
    }
   ],
   "source": [
    "print(\"Best estimator:\\n{}\".format(grid.best_estimator_))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Logistic regression step:\n",
      "LogisticRegression(C=0.1, class_weight=None, dual=False, fit_intercept=True,\n",
      "          intercept_scaling=1, max_iter=300, multi_class='warn',\n",
      "          n_jobs=None, penalty='l2', random_state=None, solver='lbfgs',\n",
      "          tol=0.0001, verbose=0, warm_start=False)\n"
     ]
    }
   ],
   "source": [
    "print(\"Logistic regression step:\\n{}\".format(\n",
    "grid.best_estimator_.named_steps[\"logisticregression\"]))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Logistic regression coefficients:\n",
      "[[-0.3914328  -0.38953715 -0.37739194 -0.38182177 -0.12563881  0.01490745\n",
      "  -0.33765699 -0.3786831  -0.05131844  0.22879421 -0.47627084  0.00635852\n",
      "  -0.35037191 -0.35459504 -0.0298673   0.19405433 -0.0047329  -0.07769192\n",
      "   0.23537054  0.23109142 -0.54040153 -0.53565333 -0.49172645 -0.48874024\n",
      "  -0.39106171 -0.12979023 -0.39291414 -0.42404076 -0.33022024 -0.13611277]]\n"
     ]
    }
   ],
   "source": [
    "print(\"Logistic regression coefficients:\\n{}\".format(\n",
    "grid.best_estimator_.named_steps[\"logisticregression\"].coef_))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Best cross-validation accuracy: 0.97\n",
      "Test set score: 0.97\n",
      "Best parameters: {'logisticregression__C': 0.1}\n"
     ]
    }
   ],
   "source": [
    "grid.fit(X_train, y_train)\n",
    "print(\"Best cross-validation accuracy: {:.2f}\".format(grid.best_score_))\n",
    "print(\"Test set score: {:.2f}\".format(grid.score(X_test, y_test)))\n",
    "print(\"Best parameters: {}\".format(grid.best_params_))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
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
  "anaconda-cloud": {},
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
   "version": "3.6.8"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/kochatice/tett/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
