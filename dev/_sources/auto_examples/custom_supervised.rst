
.. DO NOT EDIT.
.. THIS FILE WAS AUTOMATICALLY GENERATED BY SPHINX-GALLERY.
.. TO MAKE CHANGES, EDIT THE SOURCE PYTHON FILE:
.. "auto_examples/custom_supervised.py"
.. LINE NUMBERS ARE GIVEN BELOW.

.. only:: html

    .. note::
        :class: sphx-glr-download-link-note

        :ref:`Go to the end <sphx_glr_download_auto_examples_custom_supervised.py>`
        to download the full example code.

.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_examples_custom_supervised.py:


This example demonstrates how to create a custom supervised model using the
`stable_ssl` library.

.. GENERATED FROM PYTHON SOURCE LINES 5-79

.. code-block:: Python


    import hydra
    import torch
    import torch.nn.functional as F
    import torchvision
    from omegaconf import DictConfig
    from torchvision import transforms

    import stable_ssl
    from stable_ssl.supervised import Supervised


    class MyCustomSupervised(Supervised):
        def initialize_train_loader(self):
            transform = transforms.Compose(
                [
                    transforms.ToTensor(),
                    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5)),
                ]
            )
            trainset = torchvision.datasets.CIFAR10(
                root=self.config.root, train=True, download=True, transform=transform
            )
            trainloader = torch.utils.data.DataLoader(
                trainset,
                batch_size=self.config.optim.batch_size,
                shuffle=True,
                num_workers=2,
                drop_last=True,
            )
            return trainloader

        def initialize_test_loader(self):
            transform = transforms.Compose(
                [
                    transforms.ToTensor(),
                    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5)),
                ]
            )
            testset = torchvision.datasets.CIFAR10(
                root=self.config.root, train=False, download=True, transform=transform
            )
            testloader = torch.utils.data.DataLoader(
                testset,
                batch_size=self.config.optim.batch_size,
                shuffle=False,
                num_workers=2,
            )
            return testloader

        def compute_loss(self):
            """The computer loss is called during training on each mini-batch
            stable-SSL automatically stores the output of the data loader as `self.data`
            which you can access directly within that function"""
            preds = self.forward(self.data[0])
            return F.cross_entropy(preds, self.data[1])


    @hydra.main()
    def main(cfg: DictConfig):
        args = stable_ssl.get_args(cfg)

        print("--- Arguments ---")
        print(args)

        # while we provide a lot of config parameters (e.g. `optim.batch_size`), you can
        # also pass arguments directly when calling your model, they will be logged and
        # accessible from within the model as `self.config.root` (in this example)
        trainer = MyCustomSupervised(args, root="~/data")
        trainer()


    if __name__ == "__main__":
        main()


.. _sphx_glr_download_auto_examples_custom_supervised.py:

.. only:: html

  .. container:: sphx-glr-footer sphx-glr-footer-example

    .. container:: sphx-glr-download sphx-glr-download-jupyter

      :download:`Download Jupyter notebook: custom_supervised.ipynb <custom_supervised.ipynb>`

    .. container:: sphx-glr-download sphx-glr-download-python

      :download:`Download Python source code: custom_supervised.py <custom_supervised.py>`

    .. container:: sphx-glr-download sphx-glr-download-zip

      :download:`Download zipped: custom_supervised.zip <custom_supervised.zip>`


.. only:: html

 .. rst-class:: sphx-glr-signature

    `Gallery generated by Sphinx-Gallery <https://sphinx-gallery.github.io>`_
